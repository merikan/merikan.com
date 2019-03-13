---
author: peter
categories:
- technology
date: 2012-08-12T19:26:35Z
dsq_thread_id:
- "6132419051"
guid: http://blogs.merikan.com/peter/?p=494
id: 494
title: Uppgradera Linux Mint
url: /2012/08/12/uppgradera-linux-mint/
tags:
- linux
---

Skriver detta inlägg som dokumentation till mig själv.

För ett tag sedan uppgraderade jag Linux Mint från release 12 till 13. Folket på Linux Mint vill att man skall göra en [&#8220;fresh uppgrade&#8221;](http://community.linuxmint.com/tutorial/view/62) vilket innebär att man tar backup på allt sitt data och alla program för att sedan installera över den gamla releasen. Efter att installationen är klar sås skall man läsa tillbaka data och program från backupen. Jag är van att göra en paket-uppgradering istället vilket innebär att man tar en backup (?) och sedan uppgraderar. Beskrivningar för att göra detta är ganska knapphändiga eftersom de inte rekommenderar att man gör på det sättet. Efter att ha googlat runnt en stund så visste jag hur jag skulle göra.

Öppna filen _/etc/apt/sources.list_ och lägg till följande rader:

<pre class="brush: bash; title: ; notranslate" title=""># Linux Mint 13
deb http://packages.linuxmint.com/ maya main upstream import
deb http://archive.ubuntu.com/ubuntu/ precise main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ precise-updates main restricted universe multiverse
deb http://security.ubuntu.com/ubuntu/ precise-security main restricted universe multiverse
deb http://archive.canonical.com/ubuntu/ precise partner
deb http://packages.medibuntu.org/ precise free non-free
</pre>

och kommentera bort de rader som tillhör Mint 12. För mig var det följande rader:

<pre class="brush: bash; title: ; notranslate" title=""># deb http://packages.linuxmint.com/ lisa main upstream import
# deb http://archive.ubuntu.com/ubuntu/ oneiric main restricted universe multiverse
# deb http://archive.ubuntu.com/ubuntu/ oneiric-updates main restricted universe multiverse
# deb http://security.ubuntu.com/ubuntu/ oneiric-security main restricted universe multiverse
# deb http://archive.canonical.com/ubuntu/ oneiric partner
# deb http://packages.medibuntu.org/ oneiric free non-free
</pre>

Efter att detta var gjort var det bara att köra

<pre class="brush: bash; title: ; notranslate" title="">apt-get update
apt-get dist-upgrade
</pre>

Det gick inte bra första gången och jag var tvungen att ta bort &#8216;mint-search-addon&#8217;

<pre class="brush: bash; title: ; notranslate" title="">apt-get remove mint-search-addon
</pre>

Nästa gång kanske jag gör en &#8220;fresh uppgrade&#8221;. Kanske 😉