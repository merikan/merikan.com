---
author: peter
categories:
- technology
date: 2009-05-05T15:33:47Z
guid: http://blogs.merikan.com/peter/?p=348
id: 348
syntaxhighlighter_encoded:
- "1"
tags:
- blog
title: Problem with SyntaxHighlighter Evolved in WordPress MU 2.7.1
url: /2009/05/05/problem-with-syntaxhighlighter-evolved-in-wordpress-mu-271/
---

I installed SyntaxHighlighter Evolved a couple of days ago but I wasn&#8217;t sure how to use, I couldn&#8217;t get it to work. After a while I saw the following error message &#8220;Warning: htmlspecialchars_decode() expects parameter 1 to be string&#8221;. After a quick Googling for that phrase I found [this post](http://wordpress.org/support/topic/243157) that led me to the the following [bug report](http://core.trac.wordpress.org/ticket/9090) with the solution.

The line in error is line 104 in wp-includes/compat.php:  
`<br />
if ( !is_scalar( $string ) ) {<br />
`  
should be  
`<br />
if ( !is_scalar( $str ) ) {<br />
` 

I did the change and now it works perfect. 🙂