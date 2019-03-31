---
author: peter
categories:
- technology
date: 2015-10-03T22:56:35Z
dsq_thread_id:
- "5316568628"
guid: http://blogs.merikan.com/peter/?p=590
id: 590
title: Upgrade CrashPlan on Synology
aliases: ["peter/2015/10/03/upgrade-crashplan-on-synology/"]
tags:
- synology
---

<div>
  My Crashplan installation stopped working a while ago now it’s time fix it.
</div>

<div>
</div>

<div>
  <strong>Update: A few days  after I did this there was a new release available from pcloadletter at http://packages.pcloadletter.co.uk/ so I just installed it and it works perfectly. I recommend to use the packaged version instead of modifying as I did.</strong>
</div>

<div>
</div>

<div>
  On my laptop, Mac OS X, I have version 4.4.1 of Crashplan and on the server it is the 4.3.0-0033 package.
</div>

<div>
</div>

<div>
  The server log says:
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
I 09/10/15 03:37PM Upgrades available at central.crashplan.com:443
I 09/10/15 03:37PM Downloading a new version of CrashPlan.
I 09/10/15 03:58PM Download of upgrade complete - version 1430802000433.
I 09/10/15 03:58PM Installing upgrade - version 1430802000433
I 09/10/15 03:58PM Upgrade installed - version 1430802000433
I 09/10/15 03:58PM CrashPlan stopped, version 4.3.0, GUID 646867822750203914
I 10/03/15 02:13PM Synology repairing upgrade in /var/packages/CrashPlan/target/upgrade/1430802000433.1441893534258
</pre>
</div>

<div>
</div>

<div>
  You can view the log on the server or via the web interface.
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
&gt; tail -20 /var/packages/CrashPlan/target/log/history.log.0
</pre>
</div>

<div>
  If I list the update directory it looks like this:
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
&gt; ll /var/packages/CrashPlan/target/update
drwxr-xr-x    4 root     root          4096 Oct  3 14:53 .
drwxrwxrwx   11 root     root          4096 Oct  3 14:10 ..
drwxr-xr-x    4 root     root          4096 Oct  3 14:13 1430802000433.1441893534258
-rw-r--r--    1 root     root       9414120 Sep  3 21:55 1430802000433.jar
drwxr-xr-x    2 root     root          4096 Jul 28 15:49 UpgradeUI
-rw-r--r--    1 root     root           127 Jul 28 15:49 start.bat
-rw-r--r--    1 root     root            66 Oct  3 14:53 start.log
-rwxr-xr-x    1 root     root           247 Oct  3 14:13 start.sh
-rw-r--r--    1 root     root            92 Jul 28 15:49 startDesktop.bat
-rwxr-xr-x    1 root     root           312 Oct  3 14:13 startDesktop.sh
-rwxr-xr-x    1 root     root           135 Oct  3 14:13 startDesktopLinux.sh
-rwxr-xr-x    1 root     root           137 Oct  3 14:13 startDesktopSolaris.sh
-rwxr-xr-x    1 root     root            87 Oct  3 14:13 startFirst.sh
-rwxr-xr-x    1 root     root           211 Oct  3 14:13 startLinux.sh
-rwxr-xr-x    1 root     root           213 Oct  3 14:13 startSolaris.sh
</pre>
</div>

<div>
</div>

<div>
  Remove the 1430802000433.1441893534258 directory and the 1430802000433.jar file.
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
&gt; rm -rf 1430802000433.1441893534258
&gt; rm 1430802000433.1441893534258
</pre>
</div>

<div>
</div>

<div>
  Now try to start the application again; Action > Run
</div>

<div>
  It will have the status Running but since the packaged version is wrong yo will still see Version: 4.3.0-0033 in the web ui but if you look in the log file you will se another version
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
I 10/03/15 03:02PM CrashPlan started, version 4.3.3, GUID 646867822750203914
I 10/03/15 03:03PM Backup scheduled to always run
</pre>
</div>

<div>
</div>

<div>
  <b>And now there are a new versions to be downloaded, version 4.4.0 and 4.4.1</b>
</div>

<div>
  In my log file I see
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
I 10/03/15 03:03PM Upgrades available at central.crashplan.com:443
I 10/03/15 03:03PM Downloading a new version of CrashPlan.
I 10/03/15 03:04PM Download of upgrade complete - version 1435726800440.
I 10/03/15 03:04PM Download of upgrade complete - version 1435726800441.
I 10/03/15 03:04PM Installing upgrade - version 1435726800440
I 10/03/15 03:04PM Upgrade failed - version 1435726800440
</pre>
  
  <p>
    You can see that Crashplan has downloaded two versions 440 and 441. It tried to upgrade to 440 but failed. Ok, what is happening here?<br /> Crashplan is downloading the new package 1435726800440.jar and extracts it to 1435726800440.<some number>. After the file is extracted it tries to run the command ‘<em>/bin/bash upgrade.sh</em>‘ but of course it fails because bash isn’t installed. By default Synology uses Ash Shell on <a href="http://www.busybox.net/downloads/BusyBox.html">Busybox</a>. Since BusyBox provides minimalist replacements for most of the utilities you will get the following error when trying to run the script with ash:
  </p>
  
  <pre class="brush: plain; title: ; notranslate" title="">
&gt; /bin/ash upgrade.sh
upgrade.sh: line 28: syntax error: unexpected "("
</pre>
</div>

<div>
  We are happy that the script fails because it does a lot of things that will only work on linux so we have to do it another way.
</div>

<div>
  pcloadletter.co.uk have done some modification in the start and stop script to handle a upgrade but it requires a restart of the service.
</div>

<div>
</div>

<div>
  Now stop and start the service again. After the upgrade (repair) the script will exit without starting the server.
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
I 10/03/15 05:06PM Stopping CrashPlan
I 10/03/15 05:10PM Synology repairing upgrade in /var/packages/CrashPlan/target/upgrade/1435726800440.1443884368292
</pre>
</div>

<div>
  Start the server again. It will start and then stop with the following error:
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
#
# A fatal error has been detected by the Java Runtime Environment:
#
# SIGILL (0x4) at pc=0x600f2744, pid=13666, tid=1082590320
#
# JRE version: Java(TM) SE Embedded Runtime Environment (7.0_75-b13) (build 1.7.0_75-b13)
# Java VM: Java HotSpot(TM) Embedded Client VM (24.75-b04 mixed mode linux-arm )
# Problematic frame:
# C [jna266172871821141437.tmp+0x12744] ffi_prep_cif_machdep+0x16c
#
# Core dump written. Default location: /volume1/@appstore/CrashPlan/core or core.13666
#
# An error report file with more information is saved as:
# /volume1/@appstore/CrashPlan/hs_err_pid13666.log
</pre>
</div>

<div>
</div>

<div>
  In the script /var/packages/CrashPlan/scripts/start-stop-status.sh created by pcloadletter.co.uk you can see the following statements:
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
#any downloaded upgrade script will usually have failed despite the above changes  
#so ignore the script and explicitly extract the new java code using the chrisnelson.ca method
  UPGRADE_SCRIPT=`find ${OPTDIR}/upgrade -name "upgrade.sh"`
  if [ -n "${UPGRADE_SCRIPT}" ]; then
    rm ${OPTDIR}/*.pid
    SCRIPT_HOME=`dirname $UPGRADE_SCRIPT`

    #make CrashPlan log entry
    echo "I ${TIMESTAMP} Synology repairing upgrade in ${SCRIPT_HOME}" &gt;&gt; ${DLOG}

    mv ${SCRIPT_HOME}/upgrade.log ${SCRIPT_HOME}/upgrade.log.old
    UPGRADE_VER=`echo ${SCRIPT_HOME} | sed -r "s/^.*\/([0-9]+)\.[0-9]+/\1/"`
    unzip -o ${OPTDIR}/upgrade/${UPGRADE_VER}.jar "*.jar" -d ${OPTDIR}/lib/
    unzip -o ${OPTDIR}/upgrade/${UPGRADE_VER}.jar "lang/*" -d ${OPTDIR}
    mv ${SCRIPT_HOME}/upgrade.sh ${SCRIPT_HOME}/upgrade.sh.old
    exit 0
  fi
</pre>
</div>

<div>
</div>

<div>
  The script refers to Cris Nelsons instructions that you can find at <a href="http://chrisnelson.ca/2015/07/02/fixing-crashplan-4-3-0-on-synology/">http://chrisnelson.ca/2015/07/02/fixing-crashplan-4-3-0-on-synology/</a>.
</div>

<div>
  The script is looking for a file named ‘update.sh’ in the upgrade directory and in my case it found it in the 1435726800440.1443884368292 directory. Then the script is copying files from the1435726800440.jar file. After that the script is exiting so you have to start again after the upgrade.
</div>

<div>
</div>

<div>
  When looking at the script I saw a bug that will probably lead to an error in the future and it did, see abow. It is because we do not clear the lib directory before we copy the new jar files to it, this means that we can have different versions of the same java library (jar). Another bug that appeared for me was that Synology tried to upgrade the same version multiple times so I had a new directory for each try. This means that the find command in the script returns a list of update scripts instead of just one.
</div>

<div>
</div>

<div>
  After this was changed I realized that the jna.jar in update file was of version 4.1.0 and the script copied version 3.2.5 to the lib folder. Crashplan loaded the new jna.jar file and it didn’t work. Here is the bug. After I removed it Crashplan started without problems. I have taken care of that in the script below.
</div>

<div>
</div>

<div>
  This is my modified version of the update statements in the script.
</div>

<div>
  <pre class="brush: plain; title: ; notranslate" title="">
 #any downloaded upgrade script will usually have failed despite the above changes
 #so ignore the script and explicitly extract the new java code using the chrisnelson.ca method
  UPGRADE_SCRIPT=`find ${OPTDIR}/upgrade -name "upgrade.sh"`
  if [ -n "${UPGRADE_SCRIPT}" ]; then
    rm ${OPTDIR}/*.pid
    SCRIPT_HOME=`dirname $UPGRADE_SCRIPT`
    #make CrashPlan log entry
    echo "I ${TIMESTAMP} Synology repairing upgrade in ${SCRIPT_HOME}" &gt;&gt; ${DLOG}
    mv ${SCRIPT_HOME}/upgrade.log ${SCRIPT_HOME}/upgrade.log.old
    UPGRADE_VER=`echo ${SCRIPT_HOME} | sed -r "s/^.*\/([0-9]+)\.[0-9]+/\1/"`
    BACKUP_DIR=${OPTDIR}/backup.pre_${UPGRADE_VER}
    mkdir ${BACKUP_DIR}
    echo "I ${TIMESTAMP} Backing up jar files in lib folder before upgrading to ${UPGRADE_VER}" &gt;&gt; ${DLOG}
    mkdir ${BACKUP_DIR}/lib &gt;&gt; ${DLOG}
    cp -p ${OPTDIR}/lib/*.jar ${BACKUP_DIR}/lib &gt;&gt; ${DLOG}
    rm -fv ${OPTDIR}/lib/*.jar &gt;&gt; ${DLOG}
    unzip -o ${OPTDIR}/upgrade/${UPGRADE_VER}.jar "*.jar" -d ${OPTDIR}/lib/ &gt;&gt; ${DLOG}
    echo "I ${TIMESTAMP} Backing up language files in the lang folder before upgrading to ${UPGRADE_VER}" &gt;&gt; ${DLOG}
    mkdir ${BACKUP_DIR}/lang &gt;&gt; ${DLOG}
    cp -p ${OPTDIR}/lang/* ${BACKUP_DIR}/lang &gt;&gt; ${DLOG}
    unzip -o ${OPTDIR}/upgrade/${UPGRADE_VER}.jar "lang/*" -d ${OPTDIR}/lang/ &gt;&gt; ${DLOG}
    echo "I ${TIMESTAMP} Backing up so files  before upgrading to ${UPGRADE_VER}" &gt;&gt; ${DLOG}
    cp -p ${OPTDIR}/*.so ${BACKUP_DIR} &gt;&gt; ${DLOG}
    # Do not copy .so files since we might be on a arm processor
    #unzip -o ${OPTDIR}/upgrade/${UPGRADE_VER}.jar "libjtux*.so libjniwrap.so libjniwrap64.so libmd5.so jniwrap.lic " -d ${OPTDIR} &gt;&gt; ${DLOG}
    mv ${SCRIPT_HOME}/upgrade.sh ${SCRIPT_HOME}/upgrade.sh.old
    echo "I ${TIMESTAMP} CrashPlan is now upgraded to version ${UPGRADE_VER}." &gt;&gt; ${DLOG}
    # dont know why we should exit here, commenting exit command
    #exit 0
  fi

  #updates may also overwrite our native binaries
  [ -e ${OPTDIR}/bin/libffi.so.5 ] && cp -fp ${SYNOPKG_PKGDEST}/bin/libffi.so.5 ${OPTDIR}/lib
  [ -e ${OPTDIR}/bin/libjtux.so ] && cp -fp ${OPTDIR}/bin/libjtux.so ${OPTDIR}/
  # for some reason only version 3.2.5 of jna.jar is working and not 4.1.0
  mv ${OPTDIR}/lib/jna.jar ${OPTDIR}/lib/jna.jar.save
  cp -fp ${OPTDIR}/bin/jna-3.2.5.jar ${OPTDIR}/lib
 </pre>
</div>

<div>
</div>

<div>
  <b>Time to connect with my </b><b>graphical client to the Synology server</b>
</div>

<div>
</div>

<div>
  <div>
    <strong>Update: This step is no longer nececcary.</strong>
  </div>
  
  <div>
    <del>Edit the file <em>/Applications/CrashPlan.app/Contents/Resources/Java/conf/ui.properties</em> on the client computer and add the serviceHost property. Since serviceHost property is commented we will let it be and add a new one with the IP address to our NAS, e.g.:</del>
  </div>
  
  <div>
    <pre class="brush: plain; title: ; notranslate" title="">
#serviceHost=127.0.0.1
serviceHost=192.168.0.10
</pre>
  </div>
  
  <div>
    We also have to update the GUID so the client can connect to the server.
  </div>
  
  <div>
    Run the following command on the server:
  </div>
  
  <div>
    <pre class="brush: plain; title: ; notranslate" title="">
cat /var/lib/crashplan/.ui_info ;echo
</pre>
  </div>
  
  <div>
    For my server the output is:
  </div>
  
  <div>
    <pre class="brush: plain; title: ; notranslate" title="">
4243,2c2a6f90-ae27-40a2-a4b8-9d040eef7f70,0.0.0.0
</pre>
  </div>
  
  <div>
  </div>
  
  <div>
    Now edit the file /Library/Application\ Support/CrashPlan/.ui_info on the client.
  </div>
  
  <div>
    This file contains information about your client GUID so copy this information to a safe place first and then replace it with the output from your server. Don’t forget to update the 0.0.0.0 address with the address to your server. Your probably want to comment out the original property but it doesn’t work so you have to remove it. For me the file looks like this:
  </div>
  
  <div>
    <pre class="brush: plain; title: ; notranslate" title="">
4243,2c2a6f90-ae27-40a2-a4b8-9d040eef7f70,192.168.0.10
</pre>
  </div>
  
  <div>
  </div>
  
  <div>
    After doing this it’s time to start the client.
  </div>
</div>
