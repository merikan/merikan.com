---
author: peter
categories:
- technology
date: 2007-11-15T22:59:56Z
dsq_thread_id:
- "5470307857"
guid: http://blogs.merikan.com/flumride/2007/11/15/time-to-upgrade-wordpress-mu-125/
id: 109
syntaxhighlighter_encoded:
- "1"
tags:
- blog
title: Time to upgrade WordPress MU 1.2.5
aliases: ["peter/2007/11/15/time-to-upgrade-wordpress-mu-125/"]
---

A few days ago I upgraded my WordPress MU installation. It all went smoothly with no problems at all. Thank you guys at [WordPress](http://wordpress.org/) for doing such a great software.

I’m hosting my site at [Lunarpages](http://mu.wordpress.org/latest.tar.gz) and since it is a shared server you can’t use ssh, so I had to use FTP and cPanel when upgrading.

Ok, this is how I upgraded wpmu from 1.2.5 to 1.3.

  1. I know that this step isn’t always my first choice but I thought that I would try it anyway, RTFM.  
    Read the [upgrade guide](http://codex.wordpress.org/Upgrading_WordPress) at WordPress and the [extended version](http://codex.wordpress.org/Upgrading_WordPress_Extended) to.
  2. Make a backup of the installation, the wpmu folder (public_html/blogs) and the database (wpmu).
  3. I decided to upgrade a copy of my wordpress installation folder so I could easily switch to that when it was time.  
    Copy the _blogs_ folder to _wpmu13_
  4. Download the tar file from [WordPress MU](http://mu.wordpress.org/latest.tar.gz)  
    and save it on your desktop or server (in my case it was the desktop).
  5. Extract the tar file.
  6. Ok this was the part when I had to focus. According to the [upgrade guide](http://codex.wordpress.org/Upgrading_WordPress_Extended#Step_7:_Delete_the_old_WordPress_files) I should delete the old WordPress files but DO NOT DELETE everything. Since I’m upgrading a _copy_ of the WordPress folder I wasn’t so nervous. I used a ftp klient when deleting them, just select everything and then unselect those files that shouldn’t be deleted. And yes, it is in the _wpmu13_ folder I’m deleting files
  7. Now upload the WordPress MU folder that you have download and extracted in the previous step. If files already exists, just replace them. Don’t worry, remember this is just a copy of the original folder. Yes, it’s still the _wpmu13_ folder.
  8. Now it’s time to do the real upgrade step. Make sure no one is using the blog, then rename the original _blogs_ folder to _wpmu125_
  9. Rename the upgraded folder from _wpmu13_ to _blogs_.
 10. Point your browser to http://<your-domain>/wp-admin/upgrade.php to start the upgrade of the database.
 11. Done!!
