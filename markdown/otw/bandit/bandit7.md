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

We can establish a connection with the server via ssh, and using the password `z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit7@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit7@bandit.labs.overthewire.org's password: z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S


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

bandit7@bandit:~$ 
```

## Task


The password for the next level is stored in the file data.txt next to the word millionth


## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit7@bandit:~$ ls
data.txt
```

Okay, we have a file named `data.txt`. Let's print out the first 10 lines via the `head` command.

``` commandline
bandit7@bandit:~$ head data.txt
Worcester's	fyKdWWh7VVgusiIKPygHJe6TlkDHhLHl
arousal	r8mfBurE2OvHu8NFQc7mJ2x14iNjwkin
counterespionage's	4jmzYqFkqwciprPrJleFCI9tyjbXBtdt
Willard's	ctbhPNPRDGAll4Whhsrz3Mwv6qJHM8Et
midwife	Kk9VZkoTUNUfmIa031vovUN2UKksZ56S
rookie	LVU5eslJrzjrXQh4SsuSL07f7zQbwuDu
fezes	Twv6nvASc3pqIHLO9jEvrsYSKbHpSr6H
east	3iBmPOvNXWLs8L6OuLCUGQ37SEQdM8K7
preeminence's	qmAHcCxMawJxe3vHBFhL9eNWtErnoSHS
stirred	rsR2z31uFFiYWll1W1kGr38wLN6FAHgN
```

It appears as if we have two columns, where the first column contains a word and the second column a hash.

The task for this level is to find the hash next to the word "millionth". We can simply use `grep` to find
the line with "millionth" in it.

``` commandline
bandit7@bandit:~$ grep "millionth" data.txt
millionth	TESKZC0XvTetK0S9xNwm25STk5iWrBvP
```

Boom we got the flag.

