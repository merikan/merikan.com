---
title: "Upgrade to Bash 5 in MacOS"
date: 2019-04-10T14:13:13+02:00
author: Peter

#subtitle: ""
#description: ""
#image: "/files/upgrade-to-bash5-in-macos/banner-bash.png"
tags : ["macos", "bash"]
categories: ["technology"]

---
I am a long time user of zsh but I also use bash now and then but mostly when running bash scripts. I admit that I have used the default bash installation in MacOS but now was the time to upgrade to the latest bash version. With Homebrew it's super easy.

Print current version   
```bash
$ bash -version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin18)
Copyright (C) 2007 Free Software Foundation, Inc.
```

Print location of current bash
```bash
$ command -v bash
/bin/bash
```

Install bash via homebrew
```bash
$ brew install bash
```

Make sure `/usr/local/bin` is in your path before `/bin`   
for zsh use `.zprofile`
```bash
path=(
  /usr/local/{bin,sbin}
  $path[@]
)
```
and for bash use `.bashrc` or `.profile`
```bash
export PATH=/usr/local/bin:$PATH
```
Reload your current shell if you changed the path
```bash
$ exec $SHELL -l
```
Print version of our newly installed bash
```bash
$ bash -version
GNU bash, version 5.0.3(1)-release (x86_64-apple-darwin18.2.0)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
```
Print location of bash
```bash
$ command -v bash
/usr/local/bin/bash
```
Add our installed bash to the list of available login shells
```bash
$ sudo bash -c 'echo "$(command -v bash)" >> /etc/shells'
```
Print the list of available shells and make sure our version is there
```bash
$ cat /etc/shells
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/usr/local/bin/bash
```
Change your profile to use the new bash shell
```bash
$ chsh -s $(command -v bash)
```
Print your profile and make sure you are using the new bash as shell
```bash
$ dscl . -read /Users/$(whoami) UserShell
UserShell: /usr/local/bin/bash
```

That's it. Start a new terminal and use bash 5 or whatever version is the lastest 😀
