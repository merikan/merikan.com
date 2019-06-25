---
title: "Troubleshooting Hyperswitch"
date: 2019-05-26T16:17:59+02:00
author: Peter
subtitle: "time to fix this once and for all"
#description: ""
#image: ""
tags: ["macos", "hyperswitch"]
categories: ["technology"]

---
**update 20190625:** Sometimes the `login console` prevents hyperswitch to work properly, see below.

## Intro
For many years I've been using an excellent little gem called [Hyperswitch](https://bahoom.com/hyperswitch). From time to time I have had problems with it, suddenly it just stopped working and I have never found out what the reason really was. I have Googled for it and tried all sorts of things but nothing has worked and suddenly it has started to work again. I probably did something right but I have never really understood why it started to work and today after installing "MacBook Pro Supplemental Update" it stopped working again. This time I decided to get to the bottom with this and to blog about it.

## Solution
After some research I realized that my issue probably was related to the "Secure Input" feature in MacOS. When Secure Input is enabled for an application, applications like HyperSwitch, Contexts etc. will not be able to use hotkeys. 

### An application is using Secure Input
So let's find out if any application is using Secure Input. Open up a terminal and run the `ioreg` command.
```bash
$ ioreg -l -w 0 | grep SecureInput

  |   "IOConsoleUsers" = ({"kCGSSessionOnConsoleKey"=Yes,"kSCSecuritySessionID"=100008,"kCGSSessionSecureInputPID"=309,"kCGSSessionSystemSafeBoot"=No,"kCGSSessionIDKey"=257,"kCGSSessionUserNameKey"="peter","kCGSessionLoginDoneKey"=Yes,"kCGSSessionGroupIDKey"=20,"kCGSessionLongUserNameKey"="Peter Merikan","kCGSSessionAuditIDKey"=100008,"kCGSSessionLoginwindowSafeLogin"=No,"kCGSSessionUserIDKey"=501})
      |   "IOConsoleUsers" = ({"kCGSSessionOnConsoleKey"=Yes,"kSCSecuritySessionID"=100008,"kCGSSessionSecureInputPID"=309,"kCGSSessionSystemSafeBoot"=No,"kCGSSessionIDKey"=257,"kCGSSessionUserNameKey"="peter","kCGSessionLoginDoneKey"=Yes,"kCGSSessionGroupIDKey"=20,"kCGSessionLongUserNameKey"="Peter Merikan","kCGSSessionAuditIDKey"=100008,"kCGSSessionLoginwindowSafeLogin"=No,"kCGSSessionUserIDKey"=501})
```
With the help of `kCGSSessionSecureInputPID` we can see the pid `309` and that's the number of the process using Secure Input. Let's see whose pid it is.
```bash
$ ps -p 309
  PID TTY           TIME CMD
  309 ??         3:54.62 /Applications/iTerm.app/Contents/MacOS/iTerm2 -psn_0_86037
``` 
So it is `iTerm2` that uses Secure Input (if it is `loginwindow console` then see below).
Let´s kill `iTerm` and see what it looks like when it's closed.
```bash
$ kill -9 309 
```
Run `ioreg` again and see.
```bash
$  ioreg -l -w 0 | grep SecureInput                  
  |   "IOConsoleUsers" = ({"kCGSSessionOnConsoleKey"=Yes,"kSCSecuritySessionID"=100008,"kCGSSessionSecureInputPID"=309,"kCGSSessionSystemSafeBoot"=No,"kCGSSessionIDKey"=257,"kCGSSessionUserNameKey"="peter","kCGSessionLoginDoneKey"=Yes,"kCGSSessionGroupIDKey"=20,"kCGSessionLongUserNameKey"="Peter Merikan","kCGSSessionAuditIDKey"=100008,"kCGSSessionLoginwindowSafeLogin"=No,"kCGSSessionUserIDKey"=501})
      |   "IOConsoleUsers" = ({"kCGSSessionOnConsoleKey"=Yes,"kSCSecuritySessionID"=100008,"kCGSSessionSecureInputPID"=309,"kCGSSessionSystemSafeBoot"=No,"kCGSSessionIDKey"=257,"kCGSSessionUserNameKey"="peter","kCGSessionLoginDoneKey"=Yes,"kCGSSessionGroupIDKey"=20,"kCGSessionLongUserNameKey"="Peter Merikan","kCGSSessionAuditIDKey"=100008,"kCGSSessionLoginwindowSafeLogin"=No,"kCGSSessionUserIDKey"=501})
```
It still looks like `iTerm` is still using Secure Input. Time to restart MacOS to make sure it releases everything.
```bash
$ sudo shutdown -r now
```
After the restart, log in and open up the `terminal`, not iTerm, and see if `iTerm` is still using Secure Input.
```bash
$ ioreg -l -w 0 | grep SecureInput  

```
This time no application is using Secure Imput. To verify that Hyperswitch is working I started two Chrome windows and pressed Alt+Tab and yes, finally Hyperswitch is working as expected.
![Alt+Tab Chrome](/files/troubleshooting-hyperswitch/alt-tab-chrome.png)

Start up two Firefox windows just to make sure. And voila, it works to.
![Alt+Tab Firefox](/files/troubleshooting-hyperswitch/alt-tab-firefox.png)

So now we have to tell `iTerm` not to use Secure Input. We will change it with the help of the command `PlistBuddy` in the terminal. There is also a menu option we can use and I will show you that later on. But let's change via the terminal.
```bash
$ /usr/libexec/PlistBuddy -c "Set :\"Secure Input\" false"  ~/Library/Preferences/com.googlecode.iterm2.plist

```
and start `iTerm`.
```bash
$ open -a iTerm
```
After opening iTerm I just verified that Hyperswitch was working as intended.

The menu option for Secure Input for iTerm can be found at iTerm2 > Secure Keyboard Entry
![Menu Option in iTerm](/files/troubleshooting-hyperswitch/iterm2-secure-input.png)


### Application `login window` is using SecureInput
If you run `ioreg -l -w 0 | grep SecureInput` and the pid belongs to `loginwindow.app/Contents/MacOS/loginwindow console`
```
$ ps -p 92
  PID TTY           TIME CMD
   92 ??         1:27.52 /System/Library/CoreServices/loginwindow.app/Contents/MacOS/loginwindow console
```

It is because the loginwindow did not terminate when you logged in and it seems to be a common occurrence. All you have to do is to lock the screen and login again. Press `crtl  + cmd  + Q` to lock the screen and then log in again. After that, `loginwindow` should have terminated.


That's it. I finally nailed it and it feels great. 

Until next time, take care and remember, you are [great](https://www.youtube.com/watch?v=Cbk980jV7Ao).

If you find ambiguities, typos or other errors, you know what to do. 