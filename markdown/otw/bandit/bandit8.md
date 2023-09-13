# Bandit 6

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

We can establish a connection with the server via ssh, and using the password `TESKZC0XvTetK0S9xNwm25STk5iWrBvP`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit8@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit8@bandit.labs.overthewire.org's password: TESKZC0XvTetK0S9xNwm25STk5iWrBvP


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

bandit8@bandit:~$ 
```

## Task


The password for the next level is stored in the file data.txt next to the word millionth


## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit8@bandit:~$ ls
data.txt
```

Okay, we have another `data.txt` file. Let's see what the file contains with the `head` command. 

``` commandline
bandit8@bandit:~$ head data.txt
QWiiBJhqUoMj0lCD9XNrkTM1M94eIPMV
UkKkkIJoUVJG6Zd1TDfEkBdPJptq2Sn7
ITQY9WLlsn3q168qH29wYMLQjgPH9lNP
JddNHIO2SAqKPHrrCcL7yTzArusoNwrt
0dEKX1sDwYtc4vyjrKpGu30ecWBsDDa9
yvtL2C3x6iw7XOluSnoS1avXFUCsRSfg
QbKQeOYoUQULmEFOvagIzwC3EF2Gmu1S
lpRbCU2vMhGMRbAv65HhLyKEauDjtzeh
JddNHIO2SAqKPHrrCcL7yTzArusoNwrt
EN632PlfYiZbn3PhVK3XOGSlNInNE00t
```

Hmm. It looks like it just contains a bunch of hashes. According to the task for the current level, each hash appears
multiple times, except the flag. The flag only appears once in the whole file.

To solve this, we can sort the file with `sort` and then get rid of the duplicate lines with `uniq`. More specifically, 
we use the `--unique` flag with `uniq`, as the flag prints out ONLY the uniq lines. If we just used `uniq` we would
print out all lines, except their duplicates. 

``` commandline
bandit8@bandit:~$ sort data.txt | uniq --unique
EN632PlfYiZbn3PhVK3XOGSlNInNE00t
```

Boom we got the flag.

