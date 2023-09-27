# Bandit 21

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

We can establish a connection with the server via ssh, and using the password `NvEJF7oVjkddltPSrdKEFOllh9V1IBcq`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit21@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit21@bandit.labs.overthewire.org's password: NvEJF7oVjkddltPSrdKEFOllh9V1IBcq


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

bandit21@bandit:~$ 
```

## Task

--------------

A program is running automatically at regular intervals from cron, the time-based job scheduler. 
Look in /etc/cron.d/ for the configuration and see what command is being executed.

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
bandit21@bandit:~$ ls

```

``` commandline
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

`/dev/null` acts as a data sink, where any data that is sent there will be lost forever. For `cron` jobs however, this prevents
the sending of unnecessary emails. 

Okay. We can see that there is a cron job running and sends the output of `/usr/bin/cronjob_bandit22.sh` straight into the
garbage. Let's check out the shell script.

``` commandline
andit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

The password for `bandit22` gets written into the file `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`. So, we can just read it and
perhaps we'll get the flag. 

``` commandline
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff
```

Boom. We got another flag.


