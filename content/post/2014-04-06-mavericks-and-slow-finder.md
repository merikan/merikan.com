---
author: peter
categories:
- osx
- technology
date: 2014-04-06T15:27:20Z
dsq_thread_id:
- "5388605575"
guid: http://blogs.merikan.com/peter/?p=560
id: 560
title: Mavericks and slow Finder
url: /2014/04/06/mavericks-and-slow-finder/
---

Last weekend I upgraded my MBP to Mavericks. I have been waiting with the upgrade because I didn&#8217;t want to have problem with my computer so I figured I&#8217;ll wait for some bugfixes before upgrading. So last weekend I decided to take the jump and upgrade. The first thing I discovered was that HyperSwitch was not working as it should and I still have not found a solution to the problem. The next problem was that Finder was incredibly slow when showing the content in folders. Today I was googling for a solution and I encountered a solution by Snaggletooth_DE, <https://discussions.apple.com/thread/5492808?start=50&tstart=0>.

The solution is to edit the auto_master file.

  1. Open up a Terminal window
  2. Open up the file auto_master <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo nano /etc/auto_master
</pre>

  3. Comment out the row &#8220;/net  -hosts -nobrowse,hidefromfinder,nosuid&#8221; <pre class="brush: bash; light: true; title: ; notranslate" title="">#/net                    -hosts          -nobrowse,hidefromfinder,nosuid
</pre>

  4. Save the file with Ctrl+X
  5. Now restart the computer. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo restart
</pre>

After I have done the changes my Finder was as responsive as it used to be. I decided to test and see if I could reproduce the problem by uncomment the same row. So I did that and restarted. The funny thing now was that Finder was still responsive so I decided to go with that and close the case.