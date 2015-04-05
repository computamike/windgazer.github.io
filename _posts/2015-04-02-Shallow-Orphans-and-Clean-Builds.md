---
title: Shallow Orphans and Clean Builds
excerpt: How to create a clean release branch and make good use of it with Grunt.
date: 2015-04-02
permalink: /post/bitbucket2phonegap-ptIII
tags:
  - git
  - development
  - windgazer
  - bitbucket2phonegap
---

Continuing to build on our [previous article][p] that dealt with linking creating a safe
processing environment, this post is going to walk you through creating a clean release
branch. A clean orphan branch will allow you to keep the releases in the same repository.
Yet, it also allows for a place to share code without distracting build configurations,
documentation and whatever else is irrelevant to consumers of your project. I use this for
my [Phonegap][1] builds as well as my [Bower][5] releases.

I'll be building a reference implementation on *[grunt-build][6]* while working on this
article, to validate my commands and hopefully to have a nice reference build to clone
from when I start new projects.

## Working with a clean repository

I like being able to share my projects, even if sometimes I'm only sharing them with
myself. However, often I don't want to be sharing all the development clutter, like tests,
docs, and other dependencies. So, even if it's not required per se, I still end up
creating a build stage into my project. As a target for the build I'm using an *orphan
branch*. That way it's still part of the project repository, but yet stands on it's own.

### Creating an Orphan branch

First thing on the menu is creating our release branch. It'll be an 'orphan' branch and
for this one-time performance, we can't use grunt. Not to sweat though, it's not all that
complicated and, as I mentioned, a one time affair.

If you've been following my [ongoing series][s] on clean builds, you should start by cleaning
up:

```
grunt clean:all
```

And for anybody who's brave enough the next step:

```
git checkout --orphan release
git rm -rf .
```

This will first create an 'orphan' branch, a branch that has no attachment to any current
branch. The second command will throw out the current working index (all files that are in
your repository at the time of creating this branch) and creates a clean sleet. Now, don't
worry, the files are still alive in your other branches!

You might find that you've now ended up with a bunch of untracked files that weren't there
before. This is because we've also thrown `.gitignore` out with the bath-water... Clean up
what needs to go by throwing it away, add anything that remains to your `.gitignore`. And
for our closing argument:

```
git commit --allow-empty -m "Creation of Release branch"
git push origin
```

As before I've made sure this all works by copy pasting commands out of this article to
command-line, or the other way around. As such a commit of this can be view on my working
[reference repository][c1].

Don't forget to go back to your `master` branch and rebuilding whatever you need. For
those of you following my [bitbucket to phonegap series][s]:

```
git checkout master
npm install
```

### Cloning for temporary use

For me a recent discovery is the use of 'shallow cloning'. The idea is simple, instead of
getting the entire repository, you only grab it as far back as what you consider useful.
In the case of our release branch, we don't care about the past and that will work in our
advantage. A side benefit of shallow cloning is that your clone is also unaware of any of
the other branches, making for a nice quick clone operation...

Since we will want to repeat this step, over and over again, it's time to get some of the
powers of git into our grunt build. For these operations I've chosen *[grunt-git][g1]* as
the tool of choice. It comes with most of the bells and whistles of command-line git, this
mostly due to the fact that it just reads the config and then calls the command-line git.
Since you've gotten this far, I'm just assuming that you've got git installed already, so
just go ahead and install grunt-git:

```
npm install grunt-git --save-dev
```

Now that that's over with, let's start rigging up our shallow cloning in the Grunfile.js:

```javascript
gitclone: {
    release: {
        options: {
            cwd: "target/",
            branch: "release",
            depth: 1,
            repository: "<%= pkg.repository.url %>",
            directory: "release.git"
        }
    }
}
```

I'll go ahead and explain a bit of what's going on there. In the options I first set `cwd`
to the `target/` directory, that's the working directory I want to mess around in (see my
[previous article][p] on clean build directories) and adjust according to your own project
structure. It's obvious I'm picking the `release` branch, with a depth of 1 and the output
directory is going to be `release.git`. That only leaves the `repository` setting. A
common practice in grunt is to read your `package.json` and store it in the initConfig:

```javascript
grunt.initConfig({
    // Metadata.
    pkg: grunt.file.readJSON("package.json"),
    ...
});
```

That way any settings from your `package.json` are available in your Gruntfile! In this
case I'm reading the repository url from your package file. So, if you have not done so
yet, go ahead and add it to your `package.json`, it's sort of expected these days anyways:

```javascript
"repository": {
  "type": "git",
  "url": "git@bitbucket.org:windgazer/grunt-build.git"
}
```

Now, let's give it a try, shall we (the mkdir directive is optional, but added for loyal
subscribers to my [current series][s] on Automated Bitbucket to Phonegap builds)?

```
grunt mkdir gitclone:release
```

That should&#8482; have added a release.git repository to your target directory with the
last release in it, but no other branches or commit-history. Check out what's happening in
your `target/release.git` directory by running `git status` in it. To verify what I've
just done have a look at the [relevant commit][c2] on the [reference repository][6].

### Comitting results

For this trick I'm going to assume you've also gone through and implemented [ptII][p] of
[this series of posts][s]. I'll be building on top of that. So, adjust to your own situation
where appropriate...

The main assumption is that you've been able to set up a process that processes/parses
your source-files in the `target/release` directory of your project. Right next to where
we're setting up our orphan branch to receive the files. Out of this we're going to copy
everything that's relevant to our release and leave behind any crumbs left-over from the
post-processing. We're going back to our trust-worth *[grunt-contrib-clone][g2]* plugin,
for the new guys, install using:

```
npm install grunt-contrib-copy --save-dev
```

And now to set up the copy process, we'll add to our Grunfile.js initConfig:

```javascript
copy: {
    push: {
        files: [
            {
                expand: true,
                flatten: false,
                cwd: "target/release/",
                src: [
                    "assets/**.*",
                    "**/*.min.*",
                    "*.html"
                ],
                dest: "target/release.git/"
            }
        ]
    }
}
```

Of course, if you already have a copy configuration in your Gruntfile, don't add a
top-level copy, just add the push config next to the existing one. If you want to make
sure you get that right, double-check the [commit][c3] for this part in my reference
repository.

With this configuration I copy anything from assets, anything I've minified and of course
the html-files from the root. This part is all about flavour, adjust to your liking! What
I'm doing here is mostly geared towards my html5 based app development... Double-check the
results after running:

```
grunt install gitclone:release copy:push
```

Next up, we're going to have to commit this stuff to our release branch. That's basically
another two git commands that we'll have to fill out and call upon:

```javascript
gitadd: {
    release: {
        options: {
            all: true,
            cwd: "target/release.git/",
            force: false
        },
        files: {
            src: ["."]
        }
    }
},
gitcommit: {
    release: {
        options: {
            cwd: "target/release.git/",
            message: "Releasing v<%= pkg.version %>"
        },
        files: {
            src: ["."]
        }
    }
}
```

I think you should be able to get the gist of it, based on our previous cloning example.
First we'll have to `gitadd` all changes to the index, then we have to `gitcommit` them.
Using the `pkg.version` here, since we should only release once and then up the version ;)
Don't worry, that'll come up in our [next post][n]. For now it's a decent first attempt
and you should be able to modify it to suit your needs... Let's give it a spin:

```
grunt install gitclone:release copy:push gitadd:release gitcommit:release
```

Are you still with me? We're almost there, the final step is quite simple really, we have
to push the results to the repository. I've deliberately left that as a separate step, to
make sure you can verify the results before just dumping them on your repository... One
final git command, that's all it will take:

```javascript
gitpush: {
    release: {
        options: {
            cwd: "target/release.git/",
            remote: "origin",
            branch: "release",
            tags: true
        }
    }
}
```

Go ahead, add it to your initConfig in the Gruntfile.js and test it out:

```
grunt install gitclone:release copy:push gitadd:release gitcommit:release gitpush:release
```

You can see my [verified results][c4] and if you need to look at how all the code came
together so far, check [the relevant commit][c3] on the reference repository.

### Ease up on the command-line

Since I always like to verify things before I throw them out there, I tend to split my
release into two parts, local and upstream being the main channels of interrest. First
I want to do everything I can do locally, in a single command. Then I want to verify it
manually and finally hit another single command to push it upstream. As I also don't like
memorizing commands, I kept it relatively simple, the following goes into your Gruntfile:

```javascript
grunt.registerTask("release",
 "My custom release task, can be run in stages [prep|live], prep must be used before live!",
  function (type) {
    type = type ? type : "prep"; // Default release type
    grunt.task.run("release" + type);
});
grunt.registerTask(
    "releaselive",
    [
        "gitpush:release"
    ]
);
grunt.registerTask(
    "releaseprep",
    [
        "init",
        "gitclone:release",
        "copy:push",
        "gitadd:release",
        "gitcommit:release"
    ]
);
```

Now you can run:

```
grunt release:prep
```

And verify the results. If you suddenly get a failure, well, that's cause nothing changed.
After verifying you're happy with the results:

```
grunt release:live
```

To make sure you've got all the pieces put together correctly, you can check against the
[reference repository diff][c5] for this entire post.

## Shallow Orphans TL;DR

Create an orphan branch:

```
git checkout --orphan release
git rm -rf .
```

Add stuff to several project files, check out [this great diff][c5] on the [reference
repository][6] for specifics (or read the bloody post...).

```
grunt release:prep
grunt release:live
```

Use at your own risk ;)

## Next

In the [next chapter][n] we'll be discussing how to use [Grunt][4] to manage versioning.
This will be important for our [Phonegap][1] builds as each Android release must have an
incremented build version.

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/
[5]: http://bower.io/
[6]: https://bitbucket.org/windgazer/grunt-build

[g1]: https://github.com/rubenv/grunt-git
[g2]: https://github.com/gruntjs/grunt-contrib-copy

[c1]: https://bitbucket.org/windgazer/grunt-build/commits/0e301e096b87a09ba57803be7e3b271067fef1ef
[c2]: https://bitbucket.org/windgazer/grunt-build/commits/deecdb5e8f57d1e68ee29e429eed0ae60ad0a753
[c3]: https://bitbucket.org/windgazer/grunt-build/commits/c61d4fbb3f86eb4b1b1aa05ccfdfd791166defcb
[c4]: https://bitbucket.org/windgazer/grunt-build/commits/f11b193db0ba8db6848d115fd17252bc4a4b8be3
[c5]: https://bitbucket.org/windgazer/grunt-build/branches/compare/c61d4fbb3f86eb4b1b1aa05ccfdfd791166defcb..96ee47ad9827e9ed6383592cf0a61a0edc169941#diff

[p]: /post/bitbucket2phonegap-ptII/
[n]: /post/bitbucket2phonegap-ptIV/
[s]: /tags/bitbucket2phonegap/
