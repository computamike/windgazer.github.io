---
title: Using Grunt and Git for versioning
excerpt: How to use Grunt and Git together in tagging your repo with Semver approved versioning.
date: 2015-04-05
permalink: /post/bitbucket2phonegap-ptIV
tags:
  - grunt
  - development
  - windgazer
  - bitbucket2phonegap
---

In our [previous article][p] I've shown you how to commit a clean build into an Orphan
branch, keeping your build close to your source, without either cluttering up the other.
The next logical step is to start properly keeping track of versions. This article will
deal with taggin your repository to do just that.

## Semantic versioning with tags

Versioning for most projects these days is pretty much all done according to the Semantic
Versioning rules declared at [semver][6]. It is a really simple set of guidelines and both
[Node][7] and [Bower][5] make use of tags in your repository to full benefits.

This part's really easy if you've been following my [series of articles][s] on building
html5 application from your command-line all the way to [Phonegap][1]. Just in case you're
just stepping in, I'll add all the minimum required steps to get this to work. Let's start
by making sure you've got *[grunt-git][g1]* installed:

```
npm install grunt-git --save-dev
```

Now this time we're going to use the gittag task to tag our latest commit with the current
versions:

```javascript
gittag: {
    release: {
        options: {
            cwd: "target/release.git/",
            tag: "v<%= pkg.version %>"
        }
    },
    source: {
        options: {
            tag: "v<%= pkg.version %>-src"
        }
    }
}
```

To make sure I'm tagging the right commit, I assume that the latest code on the `master`
branch is the correct version for tagging the source release and whatever is on the
`release` branch must be the latest compiled release. Perhaps at some point it'll be worth
investigating making some safety checks, but for now that's up to manual verification...
Anyways, some safety can be applied, for instance by always checking out the `master`
branch before tagging:

```javascript
gitcheckout: {
    source: {
        options: {
            branch: "master"
        }
    }
}
```

This way, I can tag the source and compiled release in one fell swoop by running. Adjust
the following dish to a flavour of your own liking ;)

```
grunt clean:release mkdir gitclone:release gitcheckout:source gittag
```

Now's a good time to verify that the tag got placed on the right branches, etc. All that
remains is pushing it upstream, for those who've got some gitpush tasks set up:

```
grunt gitpush
```

Although I have no way of proving that I put those tags in the repository by way of grunt,
you can see in the [commits overview][c0] of my reference repository what sweetness the
tags bring to the table.

## Bumping the version

Great, we've released and tagged our project and source files, wat's next? Right, we'll be
needing to 'bump up' the current version number. With most of my projects I'm using both
[Bower][5] and Node, which unfortunately means I have two files with versions in it. Still
easy to change manually, but then, I'm lazy ;) So, some clever use of a little Grunt
plugin appropriately named *[grunt-bumpup][g2]* should help us solve this puzzle quick
enough:

```
grunt-bumpup --save-dev
```

Since we're not going to do anything fancy with it, all we need is to tell it which files
to bump. For safety I always add the normalize option, which is aimed at keeping all files
in sync, even if somebody's been cheeky.

```javascript
bumpup: {
    files: ["package.json","bower.json"],
    options: {
        normalize: true
    }
}
```

Go ahead, give it a test-run:

```
grunt bumpup
```

The result are awaiting you at the [relevant commit][c1] on our reference repository.

## The final dish

Let's combine [all that came before][s] with what we've learned in this article and update
our release target. I've realised a few things while writing my articles so that I've been
able to clean up my original work and create, what I believe to be, a nice a clean way of
releasing my work to my upstream repository. First of there's some changes to the
`Gruntfile`, I've quoted the entire config section and/or task that I've changed, instead
of only quoting the additions to them:

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
    },
    source: {
        options: {
            all: true,
            force: false
        },
        files: {
            src:["."]
        }
    }
},
gitcommit: {
    release: {
        options: {
            cwd: "target/release.git/",
            message: "Releasing v<%= pkg.version %> build <%= buildVersion %>",
            allowEmpty: true //In case of no changes since last dev build...
        },
        files: {
            src: ["."]
        }
    },
    source: {
        options: {
            message: "Version bump"
        },
        files: {
            src:["."]
        }
    }
},
gittag: {
    release: {
        options: {
            cwd: "target/release.git/",
            tag: "v<%= pkg.version %>"
        }
    },
    source: {
        options: {
            tag: "v<%= pkg.version %>-src"
        }
    },
    dev: {
        options: {
            cwd: "target/release.git/",
            tag: "v<%= pkg.version %>-<%= buildVersion %>"
        }
    }
}
```

```javascript
grunt.registerTask("release",
 "My custom release task, can be run in stages [prep|dev|live], prep must be used " +
 "before live!\n" +
 "'dev' will commit and push to release branch without confirmation.\n" +
 "'prep' will stash anything on current branch and checkout master branch.",
  function (type) {
    var isDev = type === "dev";
    if (!isDev) {
        grunt.task.run("releaseclean");
    } else {
        type = "prep";
    }
    type = type ? type : "prep"; // Default release type
    grunt.task.run("release" + type);
    if (isDev) {
        grunt.task.run("releasedev");
    }
});
grunt.registerTask(
    "releaselive",
    [
        "gittag:source",
        "gittag:release",
        "gitpush:release",
        "bumpup",
        "gitadd:source",
        "gitcommit:source"
    ]
);
grunt.registerTask(
    "releasedev",
    [
        "gittag:dev",
        "gitpush:release"
    ]
);
grunt.registerTask(
    "releaseclean",
    [
        "gitstash",
        "gitcheckout"
    ]
);
```

For reference, have a look at the [merge commit][c2] on my reference repository. It might
help you figure out how the pieces are supposed to fit together...

A couple of things to take note of.

```
grunt release:dev
```

The `release:dev` task is a little aggressive. It doesn't stash, or checkout anything. It
simply builds the current state of the code, throws it to the release branch and tags it
with a pre-release build version. This allows me to quickly throw what I'm working on to
my release repository and have test-build out there. The [semver][6] versioning fully
supports this kind of release as having the lowest priority.

```
grunt release:prep
```

When running `release:prep` my solution will stash any dirt on the current branch and
checkout the `master` branch. I have for myself an unbreakable rule that final releases
are always done from the `master` branch. This is a safeguard that helps me realise this
goal without accidentally throwing away code.

```
grunt release:live
```

Should only be used after running `release:prep`. This task assumes the current state of
your working directory has the latest source code release committed and working as you
want it. It then assumes that in the `target/release.git` sits a post-processed and clean
version of the same build. It goes ahead and tags both the `master` and `release` branch
with the appropriate version tags, pushes the release branch upstream and finally bumps
and commits the `master` branch to the next patch version so that you cannot (easily) do
another release with the same version.

## Next

In the [next chapter][n] we'll be wrapping up this series by kicking [Phonegap][1] into
action, thus completing our [whole cycle][s].

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/
[5]: http://bower.io/
[6]: http://semver.org/
[7]: https://nodejs.org/

[p]: /post/bitbucket2phonegap-ptIII/
[n]: /post/bitbucket2phonegap-ptV/
[s]: /tags/bitbucket2phonegap/

[g1]: https://github.com/rubenv/grunt-git
[g2]: https://github.com/darsain/grunt-bumpup

[c0]: https://bitbucket.org/windgazer/grunt-build/commits/all
[c1]: https://bitbucket.org/windgazer/grunt-build/commits/299055d8c4f5109d99e72c0a4dcbff3bf4cf1ed0
[c2]: https://bitbucket.org/windgazer/grunt-build/commits/01d6860492e4ec99b6de31b819cb7856b455fee0
