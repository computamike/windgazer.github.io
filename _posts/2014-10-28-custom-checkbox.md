---
title: Custom Checkbox using CSS3
tags:
  - css
  - html
excerpt: How to do style a checkbox? Hide it!
---

The biggest issue we have these days is that we really want to restyle form-elements. At
the same time, some of those elements are 'protected' because of user experience. Mostly
the aim is to keep the user experience close to the OS spec, and most certainly it deals
with accessibility.

However, using plain form elements just looks plain ugly! They'll never quite match the
rest of your design, unless you try to make your design match the OS. I wouldn't even want
to start thinking about that, it'll give you a head-ache like no tomorrow. So, back to
styling the form-elements.

The basics are easy, you can take apart most of them, the main exceptions being the
(multi) select, radio and checkbox. The latter is the focus of this quick show-case.
Now I won't pretend to be the first to have done this, but since I needed to do it, I
figured I might as well show the end-results!

The first and most importantly you have to realise, you can't style the checkbox! *What?
But, why am I reading this?* Well, simply put, if you can't style it, hide it :)

```css
[type="checkbox"] {
    display: none;
}
```

## So now what?

Of course, the next problem is getting back a checkbox and that's easy enough, just use
the `::before` pseudo-element. The following is an example of how to do this:

```css
[type="checkbox"] ~ label::before {
    border: 1px solid silver;
    border-radius: 6px;
    box-shadow: inset 0 0 0.2em rgba( 32,32,32 ,0.5);
    content: "\a0 ";
    display: inline-block;
    font-family: FontAwesome;
    font-size: 150%;
    line-height: 0.75em;
    margin: 0 0.5em 0 0;
    overflow: visible;
    position: relative;
    text-indent: -0.1em;
    top: 0.05em;
    width: 0.75em;
}
```

And while that gets us an acceptable looking checkbox, fiddle around until it looks like
what you want ;) and finally we move on to the next part.

## Checking the box!

Final part of the puzzle is being able to check the box. Not that hard these days, for
this special occasion we have the `:checked` pseudo-class. It's probably not so
well-known, which is mostly because, well, it only works with checkboxes and radio-buttons.

Anyways, really easy to use, it'll look something like this:

```css
[type="checkbox"]:checked ~ label::before {
    content: "\f00c";
    color: #4F4;
    text-shadow: 0.1em 0.1em 0 rgba( 32,32,32 ,0.75);
}
```

And what'd ya know, a custom styled checkbox! Great thing is, it'll still work with
keyboard and various other forms of accessibility, all of that, without a shred of
Javascript.

<iframe width="100%" height="300" src="http://jsfiddle.net/Windgazer/o3ff6rq0/embedded/result,css,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
