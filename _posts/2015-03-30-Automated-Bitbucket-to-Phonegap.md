---
title: Bitbucket to Phonegap build, Part I
excerpt: Using grunt to create a build, then triggering phonegap to actually to the work. All without going through the browser.
date: 2015-03-30
permalink: /post/bitbucket2phonegap-ptI
tags:
  - javascript
  - development
  - windgazer
---

The holy grail of development would be that you just write code and it somehow makes it to
your target device... I don't quite think we'd be able to get there yet, but in the mean
time I still aim to get closer and closer to that Utopia. In this series I aim to
document, as much for myself as anybody else on the net, how I currently get from a messy
code repository, to a clean build compiled on [build.phonegap.com][1], using [Grunt][4] on
the command-line.

Let's start at the end, and work our way back to the front. That's how I pieced it
together and I believe that's the easiest way to test that you've got everything pieced
together properly.

## Phonegap and private repositories

As I'm working on some stuff that I hope will generate enough revenue to pay back the time
I put into it, I wanted the code to live in a private repository, at least to start of
with. Now, I could have paid for some private repo's on [Github][2], but that plan is on
x amount of repositories. On the flipside [Bitbucket][3] offers plans based on the size of
the team! As they start of with free plans for tiny teams, what's not to like? Well, the
answer to that is, [Phonegap][1] doesn't integrate with [Bitbucket][3], while it has quite
convenient integration with [Github][2].

### Getting Phonegap to talk to my Bitbucket repository

In the end, the solution actually isn't very complicated at all. However, it does require
a little trust. The way to link up [Phonegap][1] to a [Bitbucket][3] repository is to
'simply' use the `https` protocol and include `user:password@` authentication in the link.
Not something I'm fond of, but at least it works...

[!["New Project"][imgI]][I]

So, go to your bitbucket repository and grab the `https` url from the clone-tool. You'd
end up with something like:

```
git clone https://loginname@bitbucket.org/myorganisation/myproject.git
```

Now you transform that link to something along the likes of:

```
https://loginname:password@bitbucket.org/myorganisation/myproject.git
```

Next step is going back to [Phonegap][1] and clicking on the new project button. Fill in
the url you've created and there ya go! If you've done everything correctly, it should now
be building a new project for you.

[!["Project listing"][imgII]][II]

### Safe for sharing

Now, if you're like me, you're not liking having your username and password in your
project listing in Phonegap! For me, the solution was quite simple. I signed up with
Bitbucket using a clean email-address intended solely for continuous integration. After
that I added that new user to my Bitbucket project having read-only access to the
repository. I figured that anybody who can see the dev-status of my project probably has
at least read-access anyway...

[!["Project settings"][imgIV]][IV]

I then modified the username and password for the repository to start using this new user.
Rebuild it, just to make sure all's still going. Phonegap will always rebuild if you hit
the `update code` button, unless the connection to your repository fails, in which case
it just silently ignores that you tried to push it's buttons ;)

[!["Project overview"][imgIII]][III]

### Phonegap to Bitbucket TL;DR

1. Create a 'Continuous Integration' user in Bitbucket
2. Give CI User read access to your repo
3. Grab https repo URL from bitbucket
4. Add CI Username and CI Password to https url
5. Create new project in Phonegap using the constructed url

## Next

In the [next chapter][n] we'll be discussing how to set up a release branch in your repository
and how to use that branch with [Grunt][4] to create your clean build.

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/

[n]: /post/bitbucket2phonegap-ptII/

[I]: https://www.flickr.com/photos/windgazer/16976845135
[imgI]: https://farm8.staticflickr.com/7621/16976845135_525e4112fa_z.jpg
[II]: https://www.flickr.com/photos/windgazer/16769470207
[imgII]: https://farm8.staticflickr.com/7645/16769470207_a113ac29bf_z.jpg
[IV]: https://www.flickr.com/photos/windgazer/16356749453
[imgIV]: https://farm9.staticflickr.com/8687/16356749453_aff1838244_z.jpg
[III]: https://www.flickr.com/photos/windgazer/16975508532
[imgIII]: https://farm8.staticflickr.com/7651/16975508532_ce87cb2edb_z.jpg
