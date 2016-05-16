---
title: Our van in time-lapse
excerpt: I'm convering a van to a camper-van with my travel-budy. This is a quick
    time-lapse of how far we've come in the last 2 months.
date: 2016-05-16
tags:
  - personal
  - travel
  - van
  - camper-van
---

Some time ago me and my travel-body bought ourselves a Mercedes Sprinter. We wanted to
travel Europe in somewhat more luxury than we've done in Australia. Going from a
Mitsubishi Pajero up to a Sprinter is a significant increase in space of course, but more
importantly, it's a lot more work :).

## How far'd we get?

So far we've stripped the van, cleaned it, primed any rusty spots we could find (not that
many, quite luckily). Then we put back the floor, added insulation, insulated the walls,
prepped the walls with laminate panels and added a roof-mounted window. Finally, in more
recent efforts, we added our beds, cooking-stove and kitchen sink. Oh, and we had the
front-seats mounted on swivel mounts plus a collapsable hand-brake.

Pfew, that's all?

### Time-lapse!

<div id="slide-show"></div>
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0013.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0014.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0021.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0029.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0035.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0036.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0052.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0053.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0058.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0059.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0065.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0102.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0107.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0111.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0118.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0133.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0223.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0225.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0226.jpg)
- ![Frame 1](https://dl.dropboxusercontent.com/u/15540151/bus/bus-0236.jpg)

<style>
#slide-show + ul {
    position: relative;
    height: 300px;
    list-style: none;
}
#slide-show + ul > li {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    opacity: 0;
    transition: opacity 500ms 750ms
}
#slide-show + ul > li.active {
    opacity: 1;
    transition: opacity 500ms 500ms
}
</style>
<script>
(function() {
    var CLASSNAME = 'active';
    var all = Array.prototype.slice.call(document.querySelectorAll('#slide-show + ul > li'));
    all[0].parentNode.addEventListener('transitionend',
        function transitionEndHandler(event) {
            var t = event.target;
            if (t.classList.contains(CLASSNAME)) {
                t.classList.toggle(CLASSNAME);
                var i = all.indexOf(t) + 1;
                if (i == all.length) {
                    i = 0;
                }
                all[i]
                    .classList.add(CLASSNAME);
            }
        }
    );
    all[0].classList.add(CLASSNAME);
})();
</script>
