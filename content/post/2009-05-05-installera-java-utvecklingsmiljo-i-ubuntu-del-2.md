---
author: peter
categories:
- technology
date: 2009-05-05T20:41:23Z
guid: http://blogs.merikan.com/peter/?p=365
id: 365
syntaxhighlighter_encoded:
- "1"
tags:
- java 
- maven 
- eclipse
title: Installera Java utvecklingsmiljö i Ubuntu, del 2
url: /2009/05/05/installera-java-utvecklingsmiljo-i-ubuntu-del-2/
---

Då var det dags för nästa del av min installation av utvecklingsmiljön i Ubuntu 9.04. Subversion och Java JDK är avklarat.

  * <span style="text-decoration: line-through">Subversion</span>
  * <span style="text-decoration: line-through">Java JDK</span>
  * Eclipse
  * Maven
  * Ant
  * Groovy och Grails
  * Firefox Plugins
  * och några fler…<img class="wp-smiley" src="../wp-includes/images/smilies/icon_wink.gif" alt=";-)" /> 

## Eclipse

**Installera Eclipse 3.4 Ganymede  
** 

  1. Gå till Eclipse [nedladdningssida](http://www.eclipse.org/downloads/) och ladda ner det paket du vill använda.
  2. Packa upp paketet <pre class="brush: bash; light: true; title: ; notranslate" title="">$ tar zxvf downloads/eclipse-jee-ganymede-SR2-linux-gtk.tar.gz
</pre>

  3. Byt namn och flytta det till _/opt_ (skapa opt om den inte finns) <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo mkdir /opt
$ mv eclipse eclipse-3.4
$ sudo mv eclipse-3.4 /opt
</pre>

  4. Skapa nu en genväg till Applications 
      1. Högerklicka på **Applications** och välj **Edit Menus**.
      2. Markera A**pplications|Programming** under **Menus:** och välj sedan **New Item**. Ett nytt fönster öppnas &#8220;Create Launcher&#8221;
      3. Fyll i värden för den Eclipse installationen du gjorde och klicka sedan på OK.  
        Name: Eclipse-3.4  
        Command: /opt/eclipse-3.4/eclipse  
        Comment: Eclipse 3.4 Ganymede
  5. Done

## Maven

Installera Maven 2.0.10

  1. Ladda ner Maven från Apache (<http://maven.apache.org/download.html>).
  2. Packa upp paketet <pre class="brush: bash; light: true; title: ; notranslate" title="">$ tar zxvf downloads/apache-maven-2.0.10-bin.tar.gz
</pre>

  3. Flytta katalogen till _/opt_ <pre class="brush: bash; light: true; title: ; notranslate" title="">$ sudo mv apache-maven-2.0.10/ /opt
</pre>

  4. Lägg nu till miljövariablerna MAVEN\_HOME och MAVEN\_OPTS. <pre class="brush: bash; light: true; title: ; notranslate" title="">$ nano ~/.bashrc
</pre>
    
    Och lägg till följande rader.
    
    <pre class="brush: bash; light: true; title: ; notranslate" title=""># Maven
export MAVEN_HOME=/opt/maven-2.0.10
export MAVEN_OPTS=-Xmx512m
export PATH=$PATH:$MAVEN_HOME/bin
</pre>
    
    Ladda om .bashrc.
    
    <pre class="brush: bash; light: true; title: ; notranslate" title="">$ source ~/.bashrc
</pre>
    
    Kontrollera att allt funkar.
    
    <pre class="brush: bash; light: true; title: ; notranslate" title="">$ mvn --version
Maven version: 2.0.10
Java version: 1.6.0_13
OS name: "linux" version: "2.6.28-11-generic" arch: "i386" Family: "unix"
</pre>

  5. Done.