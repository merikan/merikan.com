---
author: peter
categories:
- technology
date: 2007-09-16T14:30:31Z
dsq_thread_id:
- "5316568392"
id: 107
syntaxhighlighter_encoded:
- "1"
tags:
- blog
title: Installing WordPress MU on Lunarpages
aliases: ["peter/2007/09/16/installing-wordpress-mu-on-lunarpages/"]
---

I wanted to install a blogging software that everyone in the family could use. Lunarpages has the Fantastico package that contains the WordPress software. Unfortunately this is the single -user version of WordPress and I wanted each and every family member to have their own blog e.g. http://blogs.merikan.com/<name>.

This how I did it.

  1. Create a subdomain named **blogs** (blogs.yourdomain.com)
  2. Create database **wpmu**
  3. Create db user **wpmu** and add the user to the database
  4. Download the [wpmu](http://mu.wordpress.org/download/) tar file and extract it on the desktop
  5. Now upload the exploded tar file to the root directory of the newly created subdomain named blogs (/www/blogs/)
  6. Run the WordPress MU installation script by accessing http://blogs.yourdomain.om/index.php
  7. Select subdirectories.
  8. Done

You might also want to restrict blog signups.

  1. Log in as admin
  2. Select Site admin|options and restrict domain email and use yourdomain.com.
  3. or you can edit the wp-signup.php instead to only allow admin to add a new blog. Just add the following lines after get_header in wp-signup.php.  
    `if( is_user_logged_in() == false xor is_site_admin() == false ) {<br />
die( __('Sorry we are not accepting new bloggers at this time. If you already have an account - but no administrator rights - you are unable to create new blogs as well.') );<br />
}<br />
<a href="http://mu.wordpress.org/forums/topic.php?id=2361&page">for more info..</a><br />
`
