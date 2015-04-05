---
title: CSS Parallax, pt II
date: 2013-09-27 13:00
tags:
  - css
  - development
permalink: /post/css-parallax-pt-ii
excerpt: One step further on the journey to Parallax in CSS. In this installment I explain my oversight of the past and insight into the final solution...
---

In [part I][I] of this series, I attempted to create a quick parallax effect using `em` as a unit and modifying the font-size to create a different perspective of the scene. And if you've read that post you will know that I failed horribly. In retrospect this is not quite so amazing and at this point in time I went back to drawing board so to speak and tried to get a clear idea of what was haunting me.

Parallax is in effect an inverse diorama. So, what's that mean? With a diorama, the observer may change position and (s)he will see the objects in the diorama from a different angle, thus exposing, or hiding, different parts of the diorama to the observer. Since the problem with 2D is that we cannot move the point of observation, we do the opposite, we move the objects that are being observed! Quite simple really, when you put it in words…

Mathematically speaking, but then without using complex formulas… Objects that are close by compared to objects that are far away move by a different 'percentage' if you will. So what I would want to achieve is that an object which is 'close by' has a higher than average 'relative offset' while an object which is 'far away' has a lower than average 'relative offset'. Say for instance that the average is `15 * n`, an object that is close by would be at `20 * n`, while an object far away would be at `8 * n`. By changing the value of `n`, the object which is close by will move much further than the object that is far away, this would be consistent with an observer changing it's viewing point.

![Parallax using relative units][img1]

As you can see in the image that I've provided, one part of the puzzle was missing in my previous solution. In order for the base observation to be aligned correctly, all the objects need to be corrected by using an absolute unit of measurement, one that does not change with the value of `n`. This brought to live the following experiment, which is a lot closer to what I intended.

<link href="https://dl.dropboxusercontent.com/u/15540151/pebble/parallax-ptii.css" rel="stylesheet" type="text/css">
<div class="case2 test1"><div><span>Background</span></div><div><span>Foreground</span></div></div>
<div class="case2 test2"><div><span>Background</span></div><div><span>Foreground</span></div></div>
<div class="case2 test3"><div><span>Background</span></div><div><span>Foreground</span></div></div>

Needless to say, I though this was quite an exciting result. However, now the problem is that both the horizontal and the vertical axis use the same `n`, as such I would still not be able to do a full parallax effect without scripting it. So stick around for [part III][III], where I will reveal how I finally managed to do it!

[I]: {% post_url 2013-09-21-css-parallax-pt-i %}
[III]: {{ site.baseurl }}/post/css-parallax-pt-iii
[img1]: https://docs.google.com/drawings/d/15Tr8qnKLzRARAE9JCWHL2vJgt3iVqAS3BFbn6EFZwkQ/pub?w=623&amp;h=437
