---
title: Orphans, Grunt and clean Builds
excerpt: How to create a clean release branch and make good use of it with Grunt.
date: 2015-03-31
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

## Creating a clean build

*** How to use grunt to build to a 'clean' dir with 'uglify' and such

### Create a build directory

### Copy files

### Concat, minify, and so forth

### Cleaning up

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

[p]: /post/bitbucket2phonegap-ptI/
[n]: /post/bitbucket2phonegap-ptIII/
