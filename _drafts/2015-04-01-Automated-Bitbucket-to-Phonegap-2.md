---
title: Orphans, Grunt and clean Builds
excerpt: How to create a clean release branch and make good use of it with Grunt.
date: 2015-04-01
permalink: /post/bitbucket2phonegap-ptII
tags:
  - javascript
  - development
  - windgazer
---

Continuing to build on our [previous article][p] that dealt with linking [Phonegap][1] to
[Bitbucket][3], this post is going to walk you through creating a clean release branch.
A clean orphan branch will allow you to keep the releases in the same repository. Yet, it
also allows for a place to share code without distracting build configurations,
documentation and whatever else is irrelevant to consumers of your project. I use this for
my Phonegap builds as well as my [Bower][5] releases.

I'll be building a reference implementation on *[grunt-build][6]* while working on this
article, to validate my commands and hopefully to have a nice reference build to clone
from when I start new projects.

## Creating a clean build

I like being able to share my projects, even if sometimes I'm only sharing them with
myself. However, often I don't want to be sharing all the development clutter, like tests,
docs, and other dependencies. So, even if it's not required per se, I still end up
creating a build stage into my project. As a target for the build I'm using an *orphan
branch*. That way it's still part of the project repository, but yet stands on it's own.

### Create a build directory

First part of my build process is making use of a build directory. In order to do this, I
let grunt create a 'target' directory, this stems from my earlier Java years where maven
is used as a build tool. Anything generated goes into folders under the target directory
and the directory itself is going into the ignore list. Right, so, how do we do all of
that?

I'm assuming you have [Grunt][4] installed, if not, go ahead and do so now :) Also, while
you're at it, check out [grunt-init-gruntfile][7], it's really convenient...

So, for starters we're going to add the *[grunt-mkdir][g1]* dependency:

```
npm install grunt-mkdir --save-dev
```

Then add a simple setup to your `Gruntfile.js` to make you a build dir:

```javascript
mkdir: {
    target: {
        options: {
            create: [ "target" ]
        }
    }
}
```

This way, if any plugin that you're going to use doesn't automagically create the target
directory, at least it'll be there before you start using it. Now go ahead, see if it
works:

```
grunt mkdir
```

One last tweak is to add `target` to your `.gitignore` file. That way, whatever you throw
into the directory gets ignored by git, just the way [we like it][c1]...

### Copy files

The need for copying files is pretty obvious. Once you want to start minifying,
concatenation and the works, it's nice if you can do so on source-files that don't matter.
Quite unsurprisingly, copying files isn't very complicated.

Just add the *[grunt-contrib-copy][g2]* dependency:

```
npm install grunt-contrib-copy --save-dev
```

And here's an example of what I'd copy to the release folder:

```javascript
copy: {
    release: {
        files: [
            {
                expand: true, flatten: false,
                src: [
                    "*.html",
                    "fav*.*",
                    "assets/**/*",
                    "js/**/*",
                    "libs/**/*",
                    "css/**/*"
                ],
                dest: "target/release/"
            }
        ]
    }
}
```

Give it a whirl:

```
grunt copy
```

And you can find the [relevant commit][c2] on our reference repository.

### Concat, minify, and so forth

Concatenation, minification and potentially obfuscation are rather important steps in
adding a few percentages worth of performance. Coincedentaly, it also makes it harder for
third parties to run of with your hard work. Of course there's the offset of effort versus
reward and that's where build tools can make or break your efforts.

Personally I think [grunt-usemin][g3] is pretty much the holy grail on this particular
part of the build. So, go ahead, give it a go!

```
npm install grunt-contrib-concat grunt-contrib-uglify grunt-contrib-cssmin grunt-filerev --save-dev
```

Now, since we've copied our code to a nice safe playground, we can mess with it all we
want. Of course, that also means we have to point usemin to our release directory. That
way only files that are used for release will get altered. Also of note is that you must
set the proper `dest` option towards your release dir and finally, it's convenient to also
point the `stagin` option to something in your target dir.

```javascript
useminPrepare: {
    options: {
        dest: "target/release/",
        staging: "target/staging/"
    },
    html: ["target/release/index.html"]
},
usemin: {
    html: ["target/release/index.html"]
}
```

Now usemin works a little different than the usual stuff. At first `useminPrepare` will
actually create settings for the rest of the tasks, them being:

- [concat][g4] concatenates files (usually JS or CSS).
- [uglify][g5] minifies JS files.
- [cssmin][g6] minifies CSS files.
- [filerev][g7] revisions static assets through a file content hash.

I'm going to strongly advice you to read up on how usemin works on the [usemin github][g3]
pages. That's because the actual requirements of your setup are based on what you're
actually going to use. Needless to say, you don't have to use usemin in this step, you can
use whatever you want, the takeaway is that you modify the files in the `target/release`
directory, instead of the root of your project...

And again I've added this step to the [reference repository][c3] for your browser and
testing convenience.

### Cleaning up

Finally, when you're done with all of this, or actually before you do any of this, it's
important to clean the build directory. It is extremely important to clean your build dir
before doing a new build, you have to guarantee that the build uses only, and exactly,
what you intend, old stuff must be cleared.



## Committing a clean build

*** How to use the grunt-git plugin to checkout a shallow branch, build to it, and then
committing it back to the repo.

### Creating an Orphan branch

*** The basics on how to create an orphan branch with example code

### Cloning for temporary use

*** shallow clones

### Comitting results

## Next

In the [next chapter][n] we'll be discussing how to use [Grunt][4] to trigger a build on
[Phonegap][1].

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/
[5]: http://bower.io/
[6]: https://bitbucket.org/windgazer/grunt-build
[7]: https://github.com/gruntjs/grunt-init-gruntfile

[g1]: https://www.npmjs.com/package/grunt-mkdir
[g2]: https://github.com/gruntjs/grunt-contrib-copy
[g3]: https://github.com/yeoman/grunt-usemin
[g4]: https://github.com/gruntjs/grunt-contrib-concat
[g5]: https://github.com/gruntjs/grunt-contrib-uglify
[g6]: https://github.com/gruntjs/grunt-contrib-cssmin
[g7]: https://github.com/yeoman/grunt-filerev

[c1]: https://bitbucket.org/windgazer/grunt-build/commits/3160955f53d75b6ac88c5def46b962d798731937
[c2]: https://bitbucket.org/windgazer/grunt-build/commits/41821ef4636ba31a6689fe7b26c8e7c3b1241b5a
[c3]: https://bitbucket.org/windgazer/grunt-build/commits/32197515c3fdb3601873f0697adae16de0af10cf

[p]: /post/bitbucket2phonegap-ptI/
[n]: /post/bitbucket2phonegap-ptIII/
