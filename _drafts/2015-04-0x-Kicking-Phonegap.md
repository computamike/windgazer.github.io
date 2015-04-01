---
title: Using Grunt to kick Phonegap
excerpt: Telling Phonegap to update the project code, using grunt.
date: 2015-04-03
permalink: /post/bitbucket2phonegap-ptIV
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

## Next

In the [next chapter][n] we'll be discussing some advanced [Grunt][4] usage to achieve
versioning of the project, including tagging of the repository.

[1]: https://build.phonegap.com/
[2]: https://github.com/pricing/
[3]: https://bitbucket.org/plans/
[4]: http://gruntjs.com/
[5]: http://bower.io/

[p]: /post/bitbucket2phonegap-ptIII/
[n]: /post/bitbucket2phonegap-ptV/
