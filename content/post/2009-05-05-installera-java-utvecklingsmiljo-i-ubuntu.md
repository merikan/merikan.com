---
author: peter
categories:
- technology
date: 2009-05-05T15:37:31Z
guid: http://blogs.merikan.com/peter/?p=303
id: 303
syntaxhighlighter_encoded:
- "1"
tags:
- java 
- svn 
- linux
title: Installera Java utvecklingsmiljö i Ubuntu
url: /2009/05/05/installera-java-utvecklingsmiljo-i-ubuntu/
---

Dags att sätta upp utvecklingsmiljön i min nyinstallerade Ubuntu 9.04. Jag kommer att installera följande programvaror:

  * Subversion
  * Java JDK
  * Eclipse
  * Maven
  * Ant
  * Groovy och Grails
  * Firefox Plugins
  * och några fler&#8230; 😉

## Subversion

**Installera SVN CLI** 

  1. Öppna ett terminalfönster (shell) och kör följande kommando: <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo apt-get install subversion
</pre>
    
    Kontrollera att allt gick bra.
    
    <pre class="brush: bash; light: true; title: ; notranslate" title="">$ svn --version
svn, version 1.5.4 (r33841)
compiled Feb 23 2009, 20:42:53

Copyright (C) 2000-2008 CollabNet.
Subversion is open source software, see http://subversion.tigris.org/
This product includes software developed by CollabNet (http://www.Collab.Net/).
</pre>
    
    Allt gick bra och version 1.5.4 är installerad.</li> </ol> 
    
    **Installera [NautilusSVN](http://code.google.com/p/nautilussvn/).**
    
      1. Denna programvaran laddar du hem [här ifrån](http://code.google.com/p/nautilussvn/wiki/Installation) och det är Debian paketet du skall ladda hem. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ wget http://nautilussvn.googlecode.com/files/nautilussvn_0.12-1ubuntu2_all.deb
</pre>
    
      2. Installera paketet du laddade hem. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo dpkg -i nautilussvn_0.12-1ubuntu2_all.deb
</pre>
    
      3. Starta om Nautilus. Om du sedan högerklickar i Nautilus så får du fram några svn alternativ. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ nautilus -q
</pre>
        
<img class="alignnone size-medium wp-image-328" src="http://blogs.merikan.com/peter/files/2009/05/nautilussvn-300x242.png" alt="nautilussvn" width="300" height="242" srcset="https://blogs.merikan.com/peter/files/2009/05/nautilussvn-300x242.png 300w, https://blogs.merikan.com/peter/files/2009/05/nautilussvn.png 456w" sizes="(max-width: 300px) 100vw, 300px" /> </li> </ol> 
        
        Önskar att det fanns en plugin till Firefox som kunde integrera med NautilusSVN precis som [TortoiseSVN Menu](https://addons.mozilla.org/en-US/firefox/addon/3416/) gör i Windows. Himla smidigt när man surfar till ett repository och sedan kan böra ladda ner det direkt. Man kanske skulle klona TortoiseSVN så att den funkade för NautilusSVN (visst, om jag bara hade tid 😉 ).
        
        ## Java JDK
        
        Jag har tänkta att installera flera versioner av Java JDK, version 5 och 6.
        
          1. Öppna ett terminalfönster (shell) och kör följande kommando: <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo apt-get install sun-java5-jdk sun-java6-jdk
</pre>
            
            Man väljer vilken som skall vara default JDK och om man senare vill ändra detta så kör man bara följande:
            
            <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo update-alternatives --config java
</pre>
        
          2. Lägg nu till JAVA_HOME variabeln. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ nano ~/.bashrc
</pre>
            
            Lägg till följande rad
            
            <pre class="brush: bash; light: true; title: ; notranslate" title="">export JAVA_HOME=/usr/lib/jvm/java-6-sun
</pre>
            
            Ladda om .bashrc
            
            <pre class="brush: bash; light: true; title: ; notranslate" title="">$ source ~/.bashrc
</pre>
            
            Kontrollera att miljövariabeln är satt.
            
            <pre class="brush: bash; light: true; title: ; notranslate" title="">$ echo $JAVA_HOME
/usr/lib/jvm/java-6-sun
</pre>
        
        **Det kommer mera snart&#8230;.**
