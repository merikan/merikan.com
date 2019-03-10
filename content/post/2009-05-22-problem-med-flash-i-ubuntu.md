---
author: peter
categories:
- technology
date: 2009-05-22T20:06:07Z
guid: http://blogs.merikan.com/peter/?p=373
id: 373
syntaxhighlighter_encoded:
- "1"
tags:
- linux ubuntu
title: Problem med flash i Ubuntu
url: /2009/05/22/problem-med-flash-i-ubuntu/
---

Har haft lite trubbel med flash spelaren på min Ubuntu maskin. Vet att när jag satte upp den så valde jag en av tre flash spelare för Firefox. Kommer inte ihåg vilken jag valde men efter mycket dribblande i Firefox så gav jag upp. Ibland funkade det och ibland inte. Till slut hittade jag en lösning som fick det hela till att fungera. Jag gjorde enligt följande:

  1. Öppna ett terminal fönster.
  2. Kör följande kommando: <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo apt-get purge adobe-flashplugin flashplugin-nonfree gnash gnash-common libflashsupport mozilla-plugin-gnash nspluginwrapper swfdec-mozilla && sudo apt-get install flashplugin-nonfree
</pre>

  3. Starta om Firefox
  4. Testa mot <http://playrapport.se>

Fungerade klockrent efter det. 🙂