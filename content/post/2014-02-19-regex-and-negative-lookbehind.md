---
author: peter
categories:
- technology
date: 2014-02-19T09:34:08Z
dsq_thread_id:
- "6132417861"
guid: http://blogs.merikan.com/peter/?p=554
id: 554
title: Regex and negative lookbehind
url: /2014/02/19/regex-and-negative-lookbehind/
---

In my current project our application is built with Spring and we are using a couple of Spring profiles. Unfortunately we didn’t make them unique so if we deploy it with another application in the same application server we might get a name clash. We decided that we better try to use names that are specific for our application. So I started renaming all of our spring profiles by just adding the prefix apigw. to the name so it will be more unique.

auth-http-basic => apigw.auth-http-basic  
auth-x509 => apigw.auth-x509

It was pretty easy to search for all occurrences and just add the prefix, totally 52 files and 92 changes. But what if I have missed any change that should have been done? It must be some way to use a regex to search for the string ‘auth-http-basic’ that is not preceded by ‘apigw.’. After a quick search with Google I found the answer; [Negative Lookbehind](http://www.regular-expressions.info/lookaround.html).

“(?<!a)b matches a “b” that is not preceded by an “a”, using negative lookbehind. It doesn’t match <tt>cab</tt>, but matches the <tt>b</tt> (and only the <tt>b</tt>) in <tt>bed</tt> or <tt>debt</tt>.<tt>(?<=a)b</tt> (<wbr />positive lookbehind) matches the <tt>b</tt> (and only the <tt>b</tt>) in <tt>cab</tt>, but does not match <tt>bed</tt> or <tt>debt</tt>.”

This is how the regex looks for me, (?<!apigw.)(auth-http-basic|auth-x509). Just fire up SublimeText and use Find in folder..
