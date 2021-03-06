---
author: peter
categories:
- technology
date: 2009-10-04T19:28:09Z
id: 404
syntaxhighlighter_encoded:
- "1"
tags:
- android
title: Updated my phone to Android 1.6
aliases: ["peter/2009/10/04/updated-my-phone-to-android-16/"]
---

Today I updated my ADP1 to Android 1.6. I flashed the device using an updated recovery image ([read more](http://developer.htc.com/adp.html)). The process is simple, this is  how I did it.

First we need to download the radio image and the recovery image from [HTC](http://developer.htc.com/adp.html).  After the files are downloaded it’s time start the update. This is a two step process, first we will install the radio image and then the recovery image.

**Install the radio image:**

  1. Connect the phone to the computer and mount the sdcard.
  2. Copy the the radio image to the root folder of the sdcard.
  3. Rename the file to update.zip.
  4. Unmount the phone.
  5. Turn of the phone by holding down the red “Power” (hang up button) … then confirming with “OK”.
  6. Reboot the phone by holding down the Home button (House symbol) and and at the same time pressing the Power button.
  7. Wait for the triangel. Then hold down alt-key and and press L.
  8. Press alt+S
  9. The update process will now start. The following text will be displayed on the screen:  
    Android system recovery utility  
    E:Can’t open /cache/recovery/command</p> 
    Home+Back &#8211; reboot system now  
    Alt+L &#8211; toggle log t ext display  
    Alt+S &#8211; apply sdcard:update.zip  
    Alt+W &#8211; wipe data/factory reset
    
    Installing from sdcard…  
    Finding update package…  
    Opening update package…  
    Verifying update package…  
    Installing update…  
    formatting BOOT:…  
    Extracting radio image…  
    Formatting SYSTEM:…  
    Copying files…  
    Writing BOOT:…  
    Deleting files…  
    Installation complete.
    
    Press Home+Back to reboot.</li> 
    
      * Now press Home and the Back buttons and the phone will reboot. Be patient here because it will reboot a couple of times.
      * The radio image is now installed and it’s time to install the recovery image.</ol> 
    
    **Install the recovery image:**
    
      1. Connect the phone to the computer and mount the sdcard.
      2. Copy the the recovery image to the root folder of the sdcard.
      3. Rename the file to update.zip.
      4. Unmount the phone.
      5. Turn of the phone by holding down the red “Power” (hang up button) … then confirming with “OK”.
      6. Reboot the phone by holding down the Home button (House symbol) and and at the same time pressing the Power button.
      7. Wait for the triangel. Then hold down alt-key and and press L.
      8. Press alt+S.
      9. The update process will now start. The following text will be displayed on the screen:  
        Android system recovery utility  
        E:Can’t open /cache/recovery/command</p> 
        Home+Back &#8211; reboot system now  
        Alt+L &#8211; toggle log t ext display  
        Alt+S &#8211; apply sdcard:update.zip  
        Alt+W &#8211; wipe data/factory reset
        
        Installing from sdcard…  
        Finding update package…  
        Opening update package…  
        Verifying update package…  
        Installing update…  
        formatting BOOT:…  
        Extracting radio image…  
        Formatting SYSTEM:…  
        Copying files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Deleting files…  
        Writing BOOT:…  
        Installation complete.  
        Install from sdcard complete.
        
        Press Home+Back to reboot</li> 
        
          * Press Home+Back to reboot. At this point the image will be written to the phone. Now press Home and the Back buttons and the phone will reboot, at this point the image will be written to the phone. Be patient here because it will reboot a couple of times.</ol> 
        
        That was it. The Donut is now installed. 🙂
