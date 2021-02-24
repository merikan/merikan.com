---
title: "Unable to build my project with Intellij"
date: 2021-02-22T14:08:52+01:00
draft: false
author: Peter

#subtitle: ""
#description: ""
#image: ""
tags: ["macos","java", "intellij"]
categories: ["technology"]

---


Today I had some serious problems with my Intellij installation. It all started after updating to the current latest patch `2020.3.2` and I could no longer build my project inside Intellij. I had no problem building it from the command line with maven but in the ide, it refused to build. 
My problem was 
```
java: JPS incremental annotation processing is disabled. Compilation results on partial recompilation may be inaccurate. Use build process "jps.track.ap.dependencies" VM flag to enable/disable incremental annotation processing environment.

java: Compilation failed: internal java compiler error
```
After some research, I found that [Intellij had some problem with Lombok](https://github.com/rzwitserloot/lombok/issues/2592). It also looks like it has the same problem with Mastruct but in this specific project, I don't use Mapstruct. There are two ways to solve this issue, first is to upgrade Lombok to a newer version second is more of a workaround and it is to [add `-Djps.track.ap.dependencies=false` to the build process options](https://github.com/rzwitserloot/lombok/issues/2592#issuecomment-705449860).

My Lombok version was 1.18.8 so I upgraded to the current latest version 1.18.18 and it solved my problem.    

Suddenly a new problem occurred `java: Compilation failed: internal java compiler error`. It looked like it couldn't resolve dependencies to this project's submodules but I wasn't sure why. I am using jgitver and it could be therein the problem lies but it builds on the command line so it must be something with Intellij.

After a lot of trial and error, it turns out that I had different Java versions for my submodules. So after changing the project's Java version and every module to use the project version, my project finally compiled fine. I don't have time for this.....

Just another day .....
