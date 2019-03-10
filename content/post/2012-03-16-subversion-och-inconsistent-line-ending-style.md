---
author: peter
categories:
- linux
date: 2012-03-16T15:20:36Z
guid: http://blogs.merikan.com/peter/?p=482
id: 482
tags:
- linux
title: Subversion och inconsistent line ending style
url: /2012/03/16/subversion-och-inconsistent-line-ending-style/
---

Ibland händer det att radslut i filer är olika beroende på vilka verktyg man har använt och i vilken miljö man skapat filen. I windows används CRLF och i Unix används CR. För att enkelt ställa om radslut kan man använda kommandona **dos2unix** och **unix2dos**.

För att leta efter filer i en katalog och alla dess underkataloger och ändra radslut så kör man bara följande kommando.  
`<br />
find . -type f \! -exec dos2unix {} \;<br />
`