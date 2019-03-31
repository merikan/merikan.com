---
author: peter
categories:
- technology
date: 2011-02-15T19:15:38Z
guid: http://blogs.merikan.com/peter/?p=438
id: 438
tags:
- linux
- ubuntu
title: Added a second monitor to my laptop with Ubuntu
url: /2011/02/15/added-a-second-monitor-to-my-laptop/
---

I decided to move my 21&#8243; monitor from my desktop computer to my laptop that runs Ubuntu. Since I have a DVI interface on my monitor and a HDMI interface on my laptop I had to buy a adapter. I bought one at Clas Olson and it worked perfectly.

Ok, now I had to switch my primary monitor so my external monitor holds the panels instead of the built in monitor that is the default monitor. After a quick google I found [this perfect blog post](http://www.thetechrepo.com/main-articles/502-how-to-change-the-primary-monitor-in-ubuntu-or-other-linux-distributions) that describes the problem and has a nice solution. Kudos to you Andrew.

First we have to create a shell script.  
`<br />
#!/bin/bash<br />
# Author: Andrew Martin<br />
# Credit: http://ubuntuforums.org/showthread.php?t=1309247<br />
echo "Enter the primary display from the following:"			# prompt for the display<br />
xrandr --prop | grep "[^dis]connected" | cut --delimiter=" " -f1	# query connected monitors<br />
read choice								                # read the users's choice of monitor<br />
xrandr --output $choice --primary					        # set the primary monitor<br />
`  
Save it with the name monitor-switcher.sh.  
Make the script executable with ‘chmod +x monitor-switcher.sh. Now it’s time to run the script.  
`<br />
~$ ./monitor-switcher.sh<br />
Enter the primary display from the following:<br />
LVDS<br />
DFP1<br />
DFP1<br />
`  
As you can see I choose the DFP1 as my primary monitor.

Done. 🙂
