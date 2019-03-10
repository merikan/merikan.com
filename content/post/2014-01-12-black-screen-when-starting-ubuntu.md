---
author: peter
categories:
- linux
- technology
date: 2014-01-12T15:59:16Z
dsq_thread_id:
- "6132418143"
guid: http://blogs.merikan.com/peter/?p=546
id: 546
title: Black screen when starting Ubuntu
url: /2014/01/12/black-screen-when-starting-ubuntu/
---

Today I started up my Ubuntu laptop and the login screen was all black. I could see the cursor arrow but everything else was black. The laptop is dual boot with Windows and Ubuntu and I quite recently changed from Linux Mint to Ubuntu. A quick lookup with Uncle Google pointed me to this page, [My computer boots to a black screen, what options do I have to fix it? If your system is dual boot](http://askubuntu.com/questions/162075/my-computer-boots-to-a-black-screen-what-options-do-i-have-to-fix-it/162087#162087 "My computer boots to a black screen, what options do I have to fix it?"). So it was quite easy to fix and since I have Nvidia graphics card I just followed these steps:

  1. In the GRUB menu at startup, press e; then,
  2. Use the arrow keys to replace quiet splash with no splash or nomodeset.
  3. Then press the Ctrl+x key combination to boot.

That&#8217;s it. Now everything is fine and dandy