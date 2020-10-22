---
layout: post
title:  "Why I Wrote zeroconf-rs: A cross-platform wrapper around Bonjour and Avahi"
date:   2020-10-22 11:14:26 -0400
categories: posts
---

*tl;dr: I spent many hours writing a program to save me 2 seconds every time I want to copy-paste
between machines.*

If you're like me and you develop software for multiple platforms, you might have a main desktop
running Linux along with a MacBook sitting across the room. If this sounds like you, you
will have undoubtedly come across the need to share your clipboard among your multiple machines.
What's that you say? Why don't I just paste my clipboard to Slack? Well, that's what I did for many
months before arbitrarily deciding that that was no longer good enough. I wanted a way to grab my
Mac's clipboard on my Linux PC without having to roll my chair across the room to paste it into
Slack. More than that, I wanted it to be completely effortless and always-on.

Enter [zeroconf].

I started work on a program (dubbed [magiclip]) written in Rust that could solve such a dilemma.
I knew that a protocol like Bonjour/mDNS would probably be my best bet for an easy; no hassle
solution. Unfortunately, there weren't many crates that implement or wrap such a protocol and there
were no crates that contained all the features I would need to complete the project. So I decided
to roll my own.

The idea was to have a background process or daemon running that would keep an mDNS service running
to advertise to devices on my local network and simultaneously browse for services
of it's own type. All this would be tied together by a command-line client that I could use to
pull a devices clipboard. All the user would have to do is run a command, select the device they
would like to pull from, and the response would automatically be copied to the local clipboard.

```bash
$ magiclip
▸ Walker’s MacBook Pro (2) (Walkers-MacBook-Pro-2.local)
```

```bash
$ magiclip
Copied to clipboard: hello, world!
```

I started work of zeroconf on my Linux machine wrapping the Bonjour-compatible free alternative:
"Avahi". Avahi is included in most Linux distributions so I thought it would be a good choice.
After implementing a Rusty rudimentary wrapper around Avahi, I discovered that Avahi actually
offers a Bonjour compatibility layer so you don't need to implement things twice. Yikes.

Caving to the sunk-cost-fallacy and deciding that perhaps someone could use this as a library to
interact with Avahi at a lower-level one day, I decided that I would pretend this compatibility
layer does not exist, and just provide two separate implementations unified by two cross-platform
interfaces: `MdnsService` and `MdnsBrowser`.

I ended up publishing [zeroconf] on crates.io after I realized this might be useful for someone out
there and is my main motivation for writing this blog post. This is also my first blog post ever,
WOW! If you end up using it, I would love to see it in action in the wild, so let me know!

If you would like to try out [magiclip] for yourself, be my guest, but I can't make many guarantees
about it functioning on your system right now and it doesn't currently support Windows.

[zeroconf]: https://crates.io/crates/zeroconf
[magiclip]: https://github.com/windy1/magiclip
