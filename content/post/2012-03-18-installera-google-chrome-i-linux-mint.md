---
author: peter
categories:
- technology
date: 2012-03-18T18:55:29Z
dsq_thread_id:
- "5370050131"
guid: http://blogs.merikan.com/peter/?p=488
id: 488
tags:
- linux
title: Installera Google Chrome i Linux Mint
url: /2012/03/18/installera-google-chrome-i-linux-mint/
---

Har precis installerat Linux Mint på min ena dator. Har kört Ubuntu i några år nu men kände att det var dags att byta. Hur som helst så finns inte Google Chrome med i repositoryt så det går inte att installera den från den vanliga software managern. Hur som helst så är det ganska enkelt att installera i Linux ändå. Gör bara följande.

  1. Öppna ett Terminal fönster.
  2. Kör följande kommandon:`<br />
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -<br />
sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'<br />
sudo apt-get update && sudo apt-get install google-chrome-stable<br />
` 

Efter att Chrome är installerad är det bara att logga in och synka sina bokmärken, plugins, lösenord mm.

**Uppdatering**  
Hade lite trubbel att synka Google Chrome. Det var iofs lätt fixat.

  1. Välj **skiftnyckeln** längst till höger och sedan **Preferences**.
  2. Välj **Personal stuff** i menyn till vänster.
  3. Välj **Disconnect Your Google Account**.
  4. Nu är kontot bortkopplat från Chrome och det är dags att starta om Chrome.
  5. Starta nu upp Chrome igen och gå sedan in under Personal Stuff och koppla ditt konto.

&nbsp;