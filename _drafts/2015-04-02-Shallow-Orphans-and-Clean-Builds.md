---
title: Shallow Orphans and Clean Builds
excerpt: How to create a clean release branch and make good use of it with Grunt.
date: 2015-04-01
permalink: /post/bitbucket2phonegap-ptIII
tags:
  - javascript
  - development
  - windgazer
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

[p]: /post/bitbucket2phonegap-ptII/
[n]: /post/bitbucket2phonegap-ptIV/
