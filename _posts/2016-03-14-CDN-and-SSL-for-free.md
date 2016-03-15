---
title: CDN and SSL for free, am I dreaming?
excerpt: Somebody contacted me regarding my github hosted sites and how I might easily add some free SSL certs, I got curious
date: 2016-03-14
tags:
  - git
  - windgazer
  - development
---

Somewhat to my surprise I found myself reading a rather enthusiastic email from a certain
somebody working for a company that's offering free CDN and free SSL for github hosted
websites. Apparently aiming at lonesome developers, I was mildly distrustful, mostly
curious. The cat in me awoke, I wonder how many lives it has left!

## The setup

If the [https://kloudsec.com/github-pages][1] was to be believed, this isn't going to cost
me a dime and I'll be able to set this up before going to bed! Let's get crazy and give it
a go!

### Creating a new account

Well, it's simply a matter of providing my email and a password, nothing gets created yet.

### Configuring my github page

Since my github pages are already up and running, all I did here was fill in the project,
`https://github.com/windgazer/windgazer.github.io` and domain `pebble.windgazer.nl`. At
this point I got a little surprise as the form got auto-submitted while it had verified
the existence of a correct `CNAME` file in my repository. Google asked me if I wanted to
save my password and I happily continued on my way.

### Configuring my DNS

This is where it gets real. The part where I was a little concerned, but I would forge
ahead, full steam. Let's get myself SSL'd up!

- Bye bye `Deleted pebble.windgazer.nl	CNAME	windgazer.github.io.`
- Created a new 'A'-record for pebble.windgazer.nl
- I hit a snag. Although I love my service-provider dearly, it appears they don't allow me
to create my own 'txt'-records!

I was so hoping to go to sleep knowing that I now had SSL running on my domain, but alas!
I will have to call my provider and see what's up. I can only request 'txt'-records for a
domain that starts with 'www.', I'm sad now :(

## So, where's that leave me?

I had already happily deleted my CNAME record! Of course, I also already tested if my
domain would indeed run through kloudsec's CDN by first manipulating my local `hosts`
table. So, even without the 'txt'-record, it appears to work, not sure if/when I might run
into trouble on this one though :)

Onwards I marched, letting that pesky 'txt'-record be a small footnote and having a gander
at what kloudsec might throw at me next. In an effort to make people more aware of the
services they offer, they start of by indicating some 'issues' that need to be resolved. I
suppose that's not such a bad thing, although it might just as well appear like the they
didn't even manage to set up your fresh account properly without more of your intervention.

The first issue at hand is that my pages were not being optimized. I'm not 100% sure what
it is they intend to optimize though. My PNG's are all thrown at [the friendly panda of
tinypng.com][2] and my CSS is generated with SASS. I might have been a little lazy on the
minification though. I went ahead and enabled it

Next was something about uptime protection. This is where they start caching my pages. I'm
not sure how much I'll like this but I would give it a go. This stuff is all still for
free and I was asked to give the service a bit of a test-run.

## Signing off

I ended up going back to my CNAME record and hope to find some time to call my provider
during business hours. I'm not very hopeful of getting that 'txt'-record sorted, by all
appearances I'll be looking for a different DNS-host soon.

[1]: https://kloudsec.com/github-pages
[2]: https://tinypng.com/
