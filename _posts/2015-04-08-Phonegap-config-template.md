---
title: Making your Phonegap config into a template
excerpt: How to use copy filtering and a template to version your Phonegap config.
date: 2015-04-08
permalink: /post/bitbucket2phonegap-ptVI
tags:
  - phonegap
  - development
  - windgazer
  - bitbucket2phonegap
---

[So far][s] we've been working on creating a clean build directory and having [Phonegap
Build][1] automatically build from a private repository on [Bitbucket][2]. That should all
be working by now, if you've followed the series and now we're going to make a template
for the `config.xml`.

## Setting up your config.xml as a template

Let's start with a basic config file for [Phonegap Build][1].

```xml
<?xml version="1.0" encoding="UTF-8"?>
<widget
	xmlns		="http://www.w3.org/ns/widgets"
	xmlns:gap	= "http://phonegap.com/ns/1.0"
	height		="480"
	width		="640"
	viewmodes	="floating fullscreen maximized windowed"
	id			= "nl.windgazer.%safeName%"
	versionCode	="%buildVersion%"
	version		= "%version%">
	<name short="%name%">%fullName%</name>
	<description>%description%</description>
	<author email="%email%"
		href="http://oss.windgazer.nl/%safeName%/">
		%author%
	</author>
	<preference name="orientation" value="default" />
	<preference name="fullscreen" value="true" />
	<preference name="splash-screen-duration" value="1000" />
	<gap:splash src="assets/icon.png" />
	<preference name="permissions" value="none"/>
	<license href="http://windgazer.nl/casual">
		The Casual License, Copyright (c) 2015 Windgazer.nl

		As long as this software is not formally published and versioned
		as version 1.0.0 as the minimum version, full protection by
		copyright is retained with the owner (Windgazer.nl).
		Until such time as this software is published by the owner, only
		the owner may distribute copies of the software and such copies
		are deemed for personal use only.
		With exception towards the statement of fitness and warranty
		this license may be voided by a later release and licensing of
		this software and will be retroactively superceeded by any such
		license where law can enforce such action. The following statement
		of fitness and warranty must always be retained:

		THE SOFTWARE IS PROVIDED &quot;AS IS&quot;, WITHOUT WARRANTY OF
		ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE
		WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE
		AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
		HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
		WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
		FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
		OTHER DEALINGS IN THE SOFTWARE.
	</license>
	<content src="index.html"></content>
	<icon src="assets/icon.png" width="128" height="128"></icon>
</widget>
```

To facilitate my template I like to store all the settings in my `package.json` because it
will allow me to re-use the template and `Gruntfile.js` as much as possible. So, make sure
your `package.json` contains at least the following custom properties:

```javascript
"info": {
  "author": "Test Case",
  "email": "case@sensitive.com",
  "fullName": "Grunt Build",
  "safeName": "gruntBuild",
  "description": "A reference project for using grunt with my projects.",
  "id": "12345678"
}
```

Finally, we're going to perform our trick by using some advanced features of the *[grunt-
contrib-copy][g1]* plugin.

```javascript
copy: {
    pushConfig: {
        files: [
            {
                expand: true, flatten: false,
                src: [ "config.xml" ],
                dest: "target/release.git/"
            }
        ],
        options: {
            process: function(content, srcpath) {
                grunt.log.write( "Modifying " + srcpath + "\n" );
                return content
                    .replace(/%version%/g, grunt.config.get("pkg.version") )
                    .replace(/%buildVersion%/g, grunt.config.get("buildVersion") )
                    .replace(/%name%/g, grunt.config.get("pkg.name") )
                    .replace(/%fullName%/g, grunt.config.get("pkg.info.fullName") )
                    .replace(/%description%/g, grunt.config.get("pkg.info.description") )
                    .replace(/%safeName%/g, grunt.config.get("pkg.info.safeName") )
                    .replace(/%author%/g, grunt.config.get("pkg.info.author") )
                    .replace(/%email%/g, grunt.config.get("pkg.info.email") )
                ;
            }
        }
    }
}
```

I might actually upgrade this one slightly and start using a smarter regex in the future.
But for now this is simple to understand and works all the same. Don't overuse it though.

```
grunt copy:pushConfig
```

## Adding it to the build

As part of our [entire series][s], I would be remiss not to show you where to put it into
our full build script. It's not too complicated, just put it in the prep task:

```javascript
grunt.registerTask(
    "releaseprep",
    [
        "install",
        "gitclone:release",
        "copy:push",
        "copy:pushConfig",
        "gitadd:release",
        "gitcommit:release"
    ]
);
```

This will put the config file into your build directory, filtered and all. Now whenever
you build, it will carry an updated version. More importantly, the `versionCode` is always
a unique and incremental number, satisfying Android builds when you're busy testing.

As per usual, you can have a look at what the files need to look like by verifying them
against the [relevant commit][c1] of my reference repository.

## Next

In the [next chapter][n] I'll be throwing in the next bit of bonus content. We'll try and
add some json to our kickPhonegap process so that we can trigger `live` and `development`
builds properly.

[1]: https://build.phonegap.com/
[2]: https://bitbucket.org/

[c1]: https://bitbucket.org/windgazer/grunt-build/commits/d1621aa02e131d570cab98d3a064758ff836c7da

[g1]: https://github.com/gruntjs/grunt-contrib-copy

[s]: /tags/bitbucket2phonegap/
[p]: /post/bitbucket2phonegap-ptV/
[n]: /post/bitbucket2phonegap-ptVII/
