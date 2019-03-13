---
author: peter
categories:
- technology
date: 2013-08-10T16:41:07Z
dsq_thread_id:
- "5316568562"
guid: http://blogs.merikan.com/peter/?p=515
id: 515
title: “Eclipse” is damaged and can’t be opened.
url: /2013/08/10/eclipse-is-damaged-and-cant-be-opened/
tags:
- java
- osx
---

Yesterday I downloaded the latest Eclipse named Kepler to my Mac. I unzipped the package as usual and tried to launch it by clicking on the Eclipse file. I received the following error message:

<a href="http://blogs.merikan.com/peter/files/2013/08/2013-08-10_1731.png" rel="lightbox[515]"><img class="alignnone size-medium wp-image-516" alt="2013-08-10_1731" src="http://blogs.merikan.com/peter/files/2013/08/2013-08-10_1731-300x137.png" width="300" height="137" srcset="https://blogs.merikan.com/peter/files/2013/08/2013-08-10_1731-300x137.png 300w, https://blogs.merikan.com/peter/files/2013/08/2013-08-10_1731.png 420w" sizes="(max-width: 300px) 100vw, 300px" /></a>

<p dir="ltr">
  After a quick search on the internet I found the solution in a bug report at <a href="https://bugs.eclipse.org/bugs/show_bug.cgi?id=398450">Eclipse Bugs</a> . It is the OS X GateKeeper that thinks that the file is corrupt and should be moved to trash. However, the problem is easy the solve. Just follow the following steps.
</p>

  1. Open up a shell
  2. Cd yourself into the eclipse directory.
  3. Check the attributes of the directory **Eclipse.app/** <pre class="brush: bash; title: ; notranslate" title="">$ xattr Eclipse.app/
com.apple.quarantine
</pre>

  4. Remove the &#8216;com.apple.quarantine&#8217; attribute. <pre class="brush: bash; title: ; notranslate" title="">$ xattr -d com.apple.quarantine Eclipse.app/
</pre>

<p dir="ltr">
  Now you should be able to launch Eclipse.
</p>

<p dir="ltr">
  Happy coding
</p>