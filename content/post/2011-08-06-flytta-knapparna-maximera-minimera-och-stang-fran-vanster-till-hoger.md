---
author: peter
categories:
- linux
date: 2011-08-06T14:17:56Z
dsq_thread_id:
- "6143709017"
guid: http://blogs.merikan.com/peter/?p=468
id: 468
tags:
- linux ubuntu
title: Flytta knapparna &#8220;maximera&#8221;, &#8220;minimera&#8221; och &#8220;stäng&#8221;
  från vänster till höger
url: /2011/08/06/flytta-knapparna-maximera-minimera-och-stang-fran-vanster-till-hoger/
---

I Ubunti 10.04 valde man att flytta knapparna i fönstrena från höger till vänster. Varför man valde detta kan alltid diskuteras men det var tydligen för att underlätta då vi i västvärlden läser från vänster till höger. Då man tyvärr är van vid att hitta knapparna uppe till höger så blir man förvirrad varje gång som man skall klicka på dem. Då jag använder windows på jobbet 8 timmar om dagen och där sitter de till höger så lär man nog inte i första taget vänja sig att hitta knapprna till vänster.

Jag har därför valt att flytta tillbaka knapparna till höger. Det finns två sätt att göra det på och det är via det [grafiska gränssnittet](https://help.ubuntu.com/10.10/config-desktop/C/window-button-order.html) eller via kommandoraden. Jag tycker det är enklara att göra det via kommandoraden.

  1. Öppna ett terminalfönster (shell).
  2. Skriv ut de aktuella inställningarna. <pre class="brush: bash; light: true; title: ; notranslate" title="">~$ gconftool-2 --get "/apps/metacity/general/button_layout"
close,minimize,maximize:
</pre>

  3. Ändra nu inställningarna så knapparna flyttas från vänster till höger. Jag lägger även till en meny-knapp till vänster. <pre class="brush: bash; light: true; title: ; notranslate" title="">~$ gconftool-2 --set "/apps/metacity/general/button_layout" --type string "menu:minimize,maximize,close"
</pre>