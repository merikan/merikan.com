---
author: peter
categories:
- technology
date: 2012-08-12T19:46:48Z
dsq_thread_id:
- "6132418866"
guid: http://blogs.merikan.com/peter/?p=502
id: 502
title: Alldeles för tyst vid datorn
url: /2012/08/12/alldeles-for-tyst-vid-datorn/
tags:
- linux
---

Hmm, vad tyst det är när jag sitter vid datorn. Hade glömt att installera Spotify på min Linux box.  Ett par klick så är det installerat och tystnaden bruten. Ge mig Timbuktu….

Spotify har tyvärr ingen skarp version av sin programvara utan den för Linux är en preview, som varit preview ganska länge men fungerar alldeles utmärkt 😉 Ett annat alternativ är att köra Spotify i Wine men det är inget jag känner för då de tuffa killarna kör native. Spotify har en [bra beskrivning](http://www.spotify.com/se/download/previews/) hur man installerar deras preview version.

Lägg till följande rad i filen _/etc/apt/sources.list_

<pre class="brush: bash; title: ; notranslate" title="">deb http://repository.spotify.com stable non-free
</pre>

Kör följande kommando

<pre class="brush: bash; title: ; notranslate" title="">sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 94558F59
</pre>

Uppdatera paketen

<pre class="brush: bash; title: ; notranslate" title="">sudo apt-get update
</pre>

Installera spotify

<pre class="brush: bash; title: ; notranslate" title="">sudo apt-get install spotify-client
</pre>
