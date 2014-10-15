---
title: CSS Parallax, pt I
date: 2013-09-21 20:07
tags: css
permalink: /post/css-parallax-pt-i
excerpt: Parallax is probably one of the oldest (fake) 3D perspectives in use on computers, I wanted to take it on in a slightly novel way, with a minimum of javascript and as much power out of CSS as I can. However, I also wanted it to react on the mouse, not so much scrolling around...
---

<style type="text/css">
	.case1 {
		background: black;
		color: white;
		font-size: 100px;
		width: 2em;
		height: 2em;
		position: relative;
		margin: 1em auto;
	}

	.case1 div {
		position: absolute;
	}

	.case1 div:first-child {
		position: absolute;
		width: 1em;
		height: 1em;
		top: 0.5em;
		left: 0.5em;
		background: rgba(255,0,0,0.5);
	}
	.case1 div:first-child+div {
		position: absolute;
		font-size: 75%;
		width: 1.34em;
		height: 1.34em;
		top: 0.67em;
		left: 0.67em;
		background: rgba(0,0,255,0.5);
	}

	.case1 span {
		font-size: 12px;
	}

	.case1.test2 {
		font-size: 75px;
	}

	.case1.test3 {
		font-size: 125px;
	}

	div + p {
		clear: both;
		padding-top: 1em;
	}
</style>

It took me quite a bit of brain-bending to figure out a potential way of doing this. The concept I want to test is using a placement of the images based on a relative measurement, with the size of the images as an absolute measurement. By then changing the size of the relative unit, the images should displace themselves, right?

In terms of CSS this would mean I would have to do placement based on EM's, since I can alter the size of 1 EM by setting the font-size to a different pixelf-format. Also by starting out at (for instance) 100px/em, it is relatively easy to line up the images correctly...

## Test Case 1

For my first attempt I'll be using two semi-transparent boxes. That way their placement in relation to each other will be obvious and I won't have to (potentially) waste time on slicing up a cool looking picture :)

<div class="case1 test1"><div><span>Background</span></div><div><span>Foreground</span></div></div>
<div class="case1 test2"><div><span>Background</span></div><div><span>Foreground</span></div></div>
<div class="case1 test3"><div><span>Background</span></div><div><span>Foreground</span></div></div>

At this point in time I must say I was a little disappointed with my own stupidity. The twist I was looking for is still there in my mind, waiting to be put into code... This wasn't it. Clearly if you change the value of em's for one level, and then modify the position to match with the first level, no-matter how far you manipulate the parent body, the child-values will scale with it...

However, I think I'm on the right path, just using the wrong end-solution. As I mentioned, the 'twist' is waiting to be unlocked in code!! Essentially the difference in relative size is important for my trick to work. But I forgot to work into it the absolute measurement...
