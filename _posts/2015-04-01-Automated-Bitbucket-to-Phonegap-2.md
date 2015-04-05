---
title: Grunt and clean Builds
excerpt: How to create a clean release folder with Grunt and do some processing there.
date: 2015-04-01
permalink: /post/bitbucket2phonegap-ptII
tags:
  - grunt
  - development
  - windgazer
  - bitbucket2phonegap
---

## Creating a clean build

Continuing to build on our [previous article][p] that dealt with linking [Phonegap][1] to
[Bitbucket][3], this post is going to walk you through creating a clean release directory.
It's nice to have a place where you can have automated tools mess with your code, but at
the same time not messing with your actual codebase.

I'll be building a reference implementation on *[grunt-build][6]* while working on this
article, to validate my commands and hopefully to have a nice reference build to clone
from when I start new projects.

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
npm install grunt-usemin grunt-contrib-concat grunt-contrib-uglify grunt-contrib-cssmin grunt-filerev --save-dev
```

Now, since we've copied our code to a nice safe playground, we can mess with it all we
want. Of course, that also means we have to point usemin to our release directory. That
way only files that are used for release will get altered. Also of note is that you must
set the proper `dest` option towards your release dir and finally, it's convenient to also
point the `staging` option to something in your target dir.

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
what you intend, old growth must be cleared with extreme prejudice.

For this last performance we're going to use *[grunt-contrib-clean][g8]*. Quite obviously
it does what it says on the packaging, clean stuff. By now you should know the drill.

```
npm install grunt-contrib-clean --save-dep
```

And now we're adding a config to the Gruntfile.js somewhat like the following, adjust to
your tastes:

```javascript
clean: {
    release: ["target"],
    all: ["libs","node_modules","target"]
}
```

And let's give this one a whirl:

```
grunt clean:release
```

And all our hard work has been undone! Of course, if you've done everything right, this
now makes for a repeatable stunt. You can check up on the [reference repository][c4] to
double-check your work if so desired.

## The Utopia Cocktail

That was a long read and test-run, if the time I spent creating this article is any
indication. But, there's one last nugget left. For those who've been looking at the
[reference repository][6] this is not a surprise, for the rest of you this should explain
how I've mixed all of the above ingredients together.

I personally added the following task to my Gruntfile.js:

```javascript
grunt.registerTask(
    "install",
    [
        "clean:release",
        "jshint",
        "mkdir",
        "copy:release",
        "useminPrepare",
        "concat:generated",
        "cssmin:generated",
        "uglify:generated",
        "usemin"
    ]
);
```

As I hope you're now familiar with all these ingredients you can see that I first throw
away the old target directory. I then run jshint to verify my code doesn't suck, then make
sure we have a new target directory, copy the project files into it and finally run the
whole usemin pipeline.

The end result is a smooth 'single command build' that should result in a clean build of
my project, with concatenated and minified files for optimal performance! A little bonus,
install *[http-server][8]* globally (`npm install http-server -g`) and enjoy your new
build without configuring some fancy-shmancy big webserver.

```
grunt install
http-server target/release -o
```

## Clean build TL;DR

Install these dependencies

```
npm install --save-dev grunt-mkdir grunt-contrib-copy grunt-usemin grunt-contrib-concat
npm install --save-dev grunt-contrib-uglify grunt-contrib-cssmin grunt-filerev
npm install --save-dev grunt-contrib-clean
```

Add the following to your Gruntfile.js:

```javascript
clean: {
    release: ["target"],
    all: ["libs","node_modules","target"]
},
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
},
mkdir: {
    target: {
        options: {
            create: ["target"]
        }
    }
},
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

grunt.registerTask(
    "install",
    [
        "clean:release",
        "mkdir",
        "copy:release",
        "useminPrepare",
        "concat:generated",
        "cssmin:generated",
        "uglify:generated",
        "usemin"
    ]
);

```

Run:

```
grunt install
```

## Next

In the [next chapter][n] we'll be discussing how to use [Grunt][4] to put the resulting
project files into a clean repository.

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
[g8]: https://github.com/gruntjs/grunt-contrib-clean

[c1]: https://bitbucket.org/windgazer/grunt-build/commits/3160955f53d75b6ac88c5def46b962d798731937
[c2]: https://bitbucket.org/windgazer/grunt-build/commits/41821ef4636ba31a6689fe7b26c8e7c3b1241b5a
[c3]: https://bitbucket.org/windgazer/grunt-build/commits/32197515c3fdb3601873f0697adae16de0af10cf
[c4]: https://bitbucket.org/windgazer/grunt-build/commits/96ee47ad9827e9ed6383592cf0a61a0edc169941

[p]: /post/bitbucket2phonegap-ptI/
[n]: /post/bitbucket2phonegap-ptIII/
