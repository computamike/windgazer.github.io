# Testing syntax

```css
table {
    color: white;
}

.myClass {
    -webkit-columns: 3;
}

[type="checkbox"] {
    background: transparent url(myimage.png) no-repeat 50% 50%;
    border-top: 1px solid black;
}

a:hover {
    drop-shadow: 0.1em 0.1em 0.2em rgba( 32,32,32 ,0.5);
}
```

```javascript
/*global angular*/
(function() {
    "use strict";

    //TODO make context configurable
    var context = "",
        userId = ''
    ;

    function pad( n, p ) {
        return ((1<<p).toString(2) + n).slice(-p);
    }

    /**
     * Parse a String to Date, very strict format.
     * YYYY-MM-dd
     *
     * @param {String} s A string in format YYYY-MM-dd
     * @returns {Date}
     */
    function parseDate(s) {
        var parts = s.split("-")
        ;

        return new Date(~~parts[0], ~~parts[1]-1 , ~~parts[2], 0, 0, 0, 0);
    }

    /**
     * Return a date-string in the format YYYY-MM-dd based
     * on input date and offset.
     *
     * @param {Date} d The `Date` object
     * @param {int} n Optional offset
     * @returns {String}
     */
    function getDateString(d, n) {
        var offset = (typeof n !== "undefined")?n:0,
            date = new Date(
                d.getFullYear(),
                d.getMonth(),
                d.getDate() + offset,
                0, 0, 0, 0
            )
        ;

        return [
            date.getFullYear(),
            pad( date.getMonth() +1, 2),
            pad( date.getDate(), 2)
        ].join("-");
    }

    angular.module("entryServices", ["ngResource", "ngRoute"])

        .service( "EntryDAO", function( $rootScope, $resource, $q, $http ) {

            var TimeEntry = $resource(
                    context + "/api/v1/users/:userId/timeentries/summed/:entryId",
                    {
                        entryId: "@id",
                        userId: function() {
                        	return userId;
                        }
                    },
                    {
                        query: {
                            method: "GET",
                            isArray: false
                        }
                    }
                ),
                AccountRateFavourite = $resource(
            		context + "/api/v1/users/:userId/timeentries/favourites",
            		{
            			userId: "@userId"
            		}
    	        ),
                entries = [], descriptors = [],
                firstDay, lastDay, rawDates = [null, null]
            ;

            function replaceEntry( entry ) {
                //console.warn(entry);
                //TODO Fix non-existing dateString!!!
                entries = entries.filter( function( e ) {
                    return
                        e.combinedKey !== entry.combinedKey &&
                        e.executionDate !== entry.executionDate
                    ;
                } );
                entries.push( entry );
            }

            function keepAlive( ) {
                //TODO make timeout configurable
                window.setTimeout(keepAlivePayload, 60000 * 6);
            }

            function keepAlivePayload() {
                $http.head (
                    window.location.href
                )
                    .success(keepAlive)
                    .error(function() {
                        console.warn("keepalive failed!"); // jshint ignore:line
                        $rootScope.disabled = true;
                    } )
                ;
            }

            keepAlive( );

            return {
                fetchEntries: function() {
                    var deferred = $q.defer();

                    //console.log(firstDay, lastDay);
                    var resource = TimeEntry.query(
                        {
                            start: rawDates[0],
                            end: rawDates[1]
                        },
                        function() {
                            if (resource.entries) {
                                //Check for array vs single entry
                                if (resource.entries.hasOwnProperty("length")) {
                                    entries = resource.entries;
                                } else {
                                    entries = [];
                                    entries.push(resource.entries);
                                }
                            }
                            if ( resource.descriptors ) {
                                if (resource.descriptors.hasOwnProperty("length")) {
                                	descriptors = resource.descriptors;
                                } else {
                                    descriptors = [ resource.descriptors ];
                                }
                            } else {
                                descriptors = [];
                            }
                            deferred.resolve( entries );
                        })
                    ;

                    return deferred.promise;
                },
                setStartDate: function( date ) {
                    firstDay = date;
                },
                setEndDate: function( date ) {
                    lastDay = date;
                },
                setRawDates: function( dates ) {
                    rawDates = dates;
                },
                getEntries: function() {
                    return entries;
                },
                setUserID: function( newId ) {
                    userId = newId;
                },
                getUserID: function( ) {
                	return userId;
                },
                getEntry: function( desc, day ) {
                    var did = (typeof desc === "string")?desc:desc.combinedKey,
                        //TODO: polyfill for IE < 9
                        dateString = getDateString( firstDay, day ),
                        results = entries.filter(function(e) {
                            return e.executionDate && e.combinedKey === desc.combinedKey &&
                                   e.executionDate.indexOf( dateString ) === 0
                            ;
                        })
                    ;
                    //console.log(desc.combinedKey, dateString);
                    if (results.length < 1) {
                        //TODO get actual user id from somewhere...
                        var entry = new TimeEntry( {
                            "combinedKey": desc.combinedKey,
                            "userId": userId,
                            "executionDate": dateString,
                            "quantity": 0
                        } );
                        entries.push( entry ); //cache
                        results.push( entry ); //return
                    }

                    return results[0];
                },
                getEntriesByDay: function( day ) {
                    if ( !firstDay ) {
                        return [];
                    }
                    var dateString = getDateString( firstDay, day ),
                        results = entries.filter( function( e ) {
                            //console.log( e.executionDate, dateString, e.executionDate && e.executionDate.indexOf( dateString ) === 0 );
                            return e && e.executionDate && e.executionDate.indexOf( dateString ) === 0;
                        } )
                    ;

                    //console.log( results.length, results );
                    return results;
                },
                getEntriesByDesc: function( desc ) {
                    if ( !firstDay ) {
                        return [];
                    }
                    var results = entries.filter( function( e ) {
                            return e.combinedKey === desc.combinedKey;
                        } )
                    ;

                    return results;
                },
                updateEntry: function( entry ) {
                    var resource = new TimeEntry( entry ),
                        deferred = $q.defer()
                    ;

                    resource.$save( {}, function( v, h ) {
                        // console.warn( "success!!", v, h );
                        deferred.resolve( v );
                    }, function( v, h ) {
                        //console.warn( "failure!!", typeof v.data, JSON.stringify(v), h );
                        //Replace cached value, error means server returns corrected data
                        switch (v.status) {
                        case 409:
                            replaceEntry( v.data );
                            break;
                        default:
                            $rootScope.disabled = true;
                            $rootScope.connectionError = v.status;
                            console.warn( "Update of entry failed due to unexpected status: ", v.status ); // jshint ignore:line
                            break;
                        }
                        deferred.reject( v );
                    });

                    return deferred.promise;
                },
                updateFavourite: function( favourite ) {
                	//create a data entry for saving

                	var updateData = new AccountRateFavourite({
                		"combinedKey": favourite.combinedKey,
                		"favourite": favourite.favourite,
                		"userId": this.getUserID()
                	});

                	updateData.$save();
                },
                getDescriptors: function( ) {
                    return descriptors;
                },
                addDescriptor: function( favourite ) {
                    descriptors.push( favourite );
                }
            };

        } )

    ;

}());
```
