---
author: peter
categories:
- technology
date: 2007-11-14T20:58:42Z
dsq_thread_id:
- "5316568525"
guid: http://blogs.merikan.com/flumride/2007/11/14/installing-dbvisualizer-on-ubuntu/
id: 108
syntaxhighlighter_encoded:
- "1"
tags:
- java
- linux
title: Installing Dbvisualizer on Ubuntu
url: /2007/11/14/installing-dbvisualizer-on-ubuntu/
---

Since I changed from Windos to Linux Ubuntu I don&#8217;t have all of my dev tools installed. One of those tools is the great universal database tool called [DbVis](http://www.minq.se/products/dbvis/index.html) from [Minq](http://www.minq.se/). Minq doesn&#8217;t deliver DbVis as synapt package so I had to find a alternative and since I&#8217;m not a Linux blackbelt I had to do some googling. I found this [blog](http://monkeyblog.org/ubuntu/installing/) and [this](http://ubuntu.wordpress.com/2005/09/23/installing-using-an-rpm-file/) and [this](http://www.howtogeek.com/howto/ubuntu/install-an-rpm-package-on-ubuntu-linux/) that gave me a hint of what to do. I have to call the alien.

  1. First download the Dbvisualizer as a rpm package from [here](http://www.minq.se/products/dbvis/download.html).
  2. Now I have to install the alien.  
     `$ sudo apt-get update<br />
$ sudo apt-get install alien`
  3. Then convert the rpm package to a deb package with alien  
    $ sudo alien dbvis\_linux\_6\_0\_6.rpm
  4. Next, install the debian package  
    `$ sudo dpkg -i dbvis_6.0.6-2_i386.deb`
  5. Change directory to where it is installed  
    `$ cd /opt/DbVisualizer-6.0.6/`
  6. And run it  
    `$ ./dbvis`