---
author: peter
categories:
- technology
date: 2013-07-19T07:14:00Z
dsq_thread_id:
- "5509304390"
id: 511
title: No sound in OS X when playing YouTube videos in Chrome
aliases: ["peter/2013/07/19/no-sound-in-os-x-when-playing-youtube-videos-in-chrome/"]
tags:
- osx
---

It have happened a couple of times that I suddenly don’t have any sound from my speakers when watching YouTube videos in Chrome. I don’t know how and when this happens but it is easy to fix. Just open a terminal and run “sudo killall coreaudiod”. I probably should investigate why this happens but I will do that on a rainy day.

<pre class="brush: bash; title: ; notranslate" title="">$ sudo killall coreaudiod
</pre>

That’s it.
