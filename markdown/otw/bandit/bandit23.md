# Bandit 23

### Content
- **[Overview](#Overview)**
- **[Connecting](#Connecting)**
- **[Looking Around](#Looking-Around)**


## Overview

-----------------
On the OverTheWire/Bandit website, we are told how connecting to the bandit server as a specific user works.

Each user corresponds to a different level:
- Bandit0 -> Level 0
- Bandit1 -> Level 1
- ...
- Bandit33 -> Level 33

Each level is hiding a flag, which is used as the password for the following level. Meaning, one must solve `Level n` to play `Level n+1`.

We always start out connecting to the `bandit.labs.overthewire.org` server on `port 2220` for each level.
Again, the users on the server specifies the level. Each starting point is the same:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit{level}@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit{level}@bandit.labs.overthewire.org's password: 
```

The password will always be the flag of the previous level.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit23@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit23@bandit.labs.overthewire.org's password: QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G


      ,----..            ,----,          .---.
     /   /   \         ,/   .`|         /. ./|
    /   .     :      ,`   .'  :     .--'.  ' ;
   .   /   ;.  \   ;    ;     /    /__./ \ : |
  .   ;   /  ` ; .'___,/    ,' .--'.  '   \' .
  ;   |  ; \ ; | |    :     | /___/ \ |    ' '
  |   :  | ; | ' ;    |.';  ; ;   \  \;      :
  .   |  ' ' ' : `----'  |  |  \   ;  `      |
  '   ;  \; /  |     '   :  ;   .   \    .\  ;
   \   \  ',  /      |   |  '    \   \   ' \ |
    ;   :    /       '   :  |     :   '  |--"
     \   \ .'        ;   |.'       \   \ ;
  www. `---` ver     '---' he       '---" ire.org


Welcome to OverTheWire!

<snip>

bandit23@bandit:~$ 
```

## Task

--------------

A program is running automatically at regular intervals from cron, the time-based job scheduler. 
Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. 
This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
bandit23@bandit:~$ ls
bandit23@bandit:~$
```

Let's check the `/etc/cron.d` directory and take a look at the `cronjob_bandit24`.

``` commandline
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

Like the previous level, let's check out the `cronjob_bandit24.sh` file.

``` commandline
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo || exit 1
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -rf ./$i
    fi
done
```

Alrighty. We are given a very interesting bash script. Apparently, if the owner of the file is `bandit23`, then the script
will try to execute that particular program. Additionally, we ignore `.` and `..` directories, because that would suck if we 
deleted those. 
         
Let's first look at the syntax for `cron tables`. The display below was taken from the `man 5 crontab`. 

``` commandline
<snip>
Example of job definition:
.---------------- minute (0 - 59)
|  .------------- hour (0 - 23)
|  |  .---------- day of month (1 - 31)
|  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
|  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
|  |  |  |  |
m h dom mon dow usercommand
17 * * * *  root  cd / && run-parts --report /etc/cron.hourly
25 6 * * *  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6 * * 7  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6 1 * *  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
<snip>
```

There are 5 asteriks in each line, which represent a *make-shift* clock for the execution that particular command.
For instance, the first column stands for the minutes **AFTER** the `0th` minute, meaning `m` minutes after the beginning
of each hour. However, if we just have an asteriks we execute the program every minute. 

Let's take the `3rd` command in the `crontab` above and analyze when it executes and by who.

``` commandline
47 6 * * 7  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
```

This command will execute every `sunday` at the `6th hour` of the day and the `47th minute` of the hour, by the root user.
So, it will execute **every sunday at 6:47am by root user**.


Now, let's analyze the actual `crontab` file on the server.

``` commandline
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

Alright. So we have a script that gets run `every minute` by user `bandit24`. Since we have analyzed the script above, we know
that if the owner of a file is `bandit23` in the directory `/var/spool/bandit24/foo` then the file gets executed by user `bandit24`.

This is actually very interesting, because that means we can create a file as user `bandit23` and move it to directory
`/var/spool/bandit24/foo` to have the file be executed. `bandit24` would have access to the password file of `bandit24`,
so we can have the `crontab` read the password for us and write it to a file that we can read. 

Let's create a temporary directory using the `mktemp -d` command. Let's make this directory accessible to everyone via `chmod 777`. 
This is not recommended for general directories, but we are hackers and need the flag **quickly**.

``` commandline
bandit23@bandit:~$ mktemp -d
/tmp/tmp.02TwNDCOOI
bandit23@bandit:~$ cd /tmp/tmp.02TwNDCOOI
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ chmod 777 /tmp/tmp.02TwNDCOOI
```

We also need to create two more files: password and trojan_horsey.sh

``` commandline
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ touch password trojan_horsey.sh
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ chmod 777 password ./trojan_horsey.sh 
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ ls
password  trojan_horsey.sh
```

Alright. We have the two files. Let's create our super magical hacker script in the `trojan_horsey.sh` file via `nano`.

``` commandline
#! /bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/tmp.02TwNDCOOI/password
```

Now we `copy` the file to the `/var/spool/bandit24/foo` directory and see what happens.

``` commandline
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ cp trojan_horsey.sh /var/spool/bandit24/foo/trojan_horsey.sh
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ ls -l password 
-rwxrwxrwx 1 bandit23 bandit23 33 Sep 24 19:22 password
```

Woow. It looks like we got something in the file. Let's print it out.

``` commandline
bandit23@bandit:/tmp/tmp.02TwNDCOOI$ cat password 
VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar
```

Boom. We got the flag for the level.


