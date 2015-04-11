---
title: Using Grunt to kick Phonegap
excerpt: How to make Phonegap update and rebuild based on your latest repository commit.
date: 2015-04-07
permalink: /post/bitbucket2phonegap-ptV
tags:
  - phonegap
  - development
  - windgazer
  - bitbucket2phonegap
---

Continuing to build on our [previous article][p] that dealt with versioning of our source
and release code, this article will reveal how to give [Phonegap][1] a kick in the but and
get it into action, without having to logon to the website manually.

*Revised on 2015-04-12, discovered blogged http-task was incomplete*

## The Phonegap API

Maybe not the first thing people would think of checking but PhoneGap Build does have [an
API][5]. Essentially, it allows you to do anything you would usually do on their website,
using some sweet `REST` calls and sharing information using `JSON`. They've kept it
simple, so simple in fact that it uses basic authentication. Perhaps not the most secure
way of dealing with authentication, but it makes what I'm about to show you a heck of a
lot easier.

First of, have read on the API documentation about [updating an existing app][a1]. Even as
it states it is for 'updating' an app, it will also cause a rebuild. Which is nice, as it
is nice and easy. Let's go ahead and test it, shall we?

### Basic testing in the browser

You'll need to get your app id from the [https://build.phonegap.com/apps][6] overview.

[!["Project listing"][imgII]][II]

It's right there, top-left of your project information :) I've blacked it out in my
screenie, not quite ready to share all of my secrets with you. Anyways, armed with that id
we can now complete our url! So just open it up in your browser:

```
https://build.phonegap.com/api/v1/apps/[your app id goes here ;)]
```

The phonegap api will now ask for username and password, just use those that you use for
the normal web interface. If you did it all correct, you should be presented with a screen
full of JSON, outlining all info Phonegap has on your project.

### Taking a leap back to Grunt

Armed with your `app id`, `username` and `password` we should start looking back at grunt
now. For starters, I'd like to introduce you to *[grunt-http][g1]*, a plugin that keeps
things streight-forward.

```
npm install grunt-http --save-dev
```

We're going to use `grunt-http` to do our earlier request again, only this time we'll use
a `put` request and we'll also inline the username and password. There's one tricky bit
here, Phonegap Build uses emails as a username and that messes a bit with the url syntax
for this case. Make sure that you escape the `@` in your username with `%40`.

```javascript
http: {
    kickPhonegap: {
        options: {
            url: "https://[name]%40[domain]:[password]@build.phonegap.com/api/v1/apps/[id]",
            method: "PUT"
        },
        dest: "target/release/phonegap.response.json"
    }
}
```

Now, if you run this task on grunt

```
grunt http:kickPhonegap
```

And update your apps overview page, you'll notice that the last build is set to 1 minute
ago! At this point I was quite excited. Although I really don't like putting my password
into a url, or even worse, into a repository...

### Creating a basic auth 'hash'

We're going to do a little prep-work again. Using the javascript console of your browser,
run the following script:

```javascript
btoa("[username]:[password]");
```

For instance `btoa( "dodo@extinct.com:d0d0XL" )` would end up looking like
`ZG9kb0BleHRpbmN0LmNvbTpkMGQwWEw=`. You should copy that hash unto your clipboard. It's
only BASE64 encoded, so that's in no way safe for the repository. Instead we're going to
save the hash in a file in our user directory! Use any method you have at your disposal
to create a file in your user directory and put the hash into it. An example command for a
`*`nix flavoured OS is:

```
printf [hash]>~/.phonegap.auth
```

I'm also working towards having a common `Gruntfile.js` for all of my projects, so I've
moved my app id to the `package.json` file in the `info.id` custom property.

### Using headers to hide our Auth request

So, now we've prepped a few things we can get away with a slightly more advanced http
request. Instead of putting our basic auth information on the url, we can hide it away in
a pair of headers.

```javascript
http: {
    kickPhonegap: {
        options: {
            url: "https://build.phonegap.com/api/v1/apps/<%= pkg.info.id %>",
            method: "PUT",
            headers: {
                "Authorization": "Basic " +
                    "<%= grunt.file.read( process.env[\"HOME\"] + \"/.phonegap.auth\") %>",
                "Accept": "*/*"
            },
            form: {
                data: {
                    pull: true
                }
            }
        },
        dest: "target/release/phonegap.response.json"
    }
}
```

To be fair, I've never tested this on a windows machine, so let me know if it works, or
not! And if not, what you might have done to make it work anyways :)

```
grunt http:kickPhonegap
```

The nice thing is by reading the file only when we actually run the kickPhonegap task, we
also make sure that anybody that just wants to work on the project without interacting
with Phonegap, doesn't have to worry about not having that `auth` file.

You can verify you've done your changes correctly on the [relevant commit][c1] of my
[reference repository][7].

## Kicking Phonegap TL;DR

Create a username/password hash using `btoa("[username]:[password]");` in your browser
console. Paste the result into a file named `~/.phonegap.auth`.

Find your app-id on the [Phonegap Build apps overview][6] and put it into your
`package.json` under the `info.id` custom property.

Install [grunt-http][g1] and copy past the following config:

```javascript
http: {
    kickPhonegap: {
        options: {
            url: "https://build.phonegap.com/api/v1/apps/<%= pkg.info.id %>",
            method: "PUT",
            headers: {
                "Authorization": "Basic " +
                    "<%= grunt.file.read( process.env[\"HOME\"] + \"/.phonegap.auth\") %>",
                "Accept": "*/*"
            }
        },
        dest: "target/release/phonegap.response.json"
    }
}
```

Now just run:

```
grunt http:kickPhonegap
```

## Next

In the [next chapter][n] I'll be throwing in a bit of bonus content. Phonegap doesn't use
the version in your git-tags, or your package.json :( So, we'll set up our `config.xml` to
be a template, with it's relevant parts being filled in using a filtered copy. While at it
let's also try and add some json to our kickPhonegap process so that we can trigger `live`
and `development` builds properly.

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/
[5]: http://docs.build.phonegap.com/en_US/developer_api_api.md.html
[6]: https://build.phonegap.com/apps
[7]: https://bitbucket.org/windgazer/grunt-build

[p]: /post/bitbucket2phonegap-ptIV/
[n]: /post/bitbucket2phonegap-ptVI/

[a1]: http://docs.build.phonegap.com/en_US/developer_api_write.md.html#_put_https_build_phonegap_com_api_v1_apps_id

[c1]: https://bitbucket.org/windgazer/grunt-build/branches/compare/29278f41cb48a1f1ad049770f8db1df55b199793..ab3f5b774ad80bdac5a18da88f1fd19a26cdb009#diff

[g1]: https://github.com/johngeorgewright/grunt-http

[II]: https://www.flickr.com/photos/windgazer/16769470207
[imgII]: https://farm8.staticflickr.com/7645/16769470207_a113ac29bf_z.jpg
