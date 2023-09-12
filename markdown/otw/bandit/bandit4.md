# Bandit 0

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

We can establish a connection with the server via ssh, and using the password `2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit4@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit4@bandit.labs.overthewire.org's password: 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe


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

bandit4@bandit:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit4@bandit:~$ ls
inhere/
```

Like the previous level, we have a directory named "inhere" which we can go into using `cd`.


``` commandline
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

Okay, we have a bunch of files all starting with "-" again. This is a throwback to Level 1, where we had to use `./` 
to force `cat` to recognize "-" as part of the filename. We can use the `ls` command with `-la` flags to get some more 
details about the files in the directory.

``` commandline 
bandit4@bandit:~/inhere$ ls -la
total 48
drwxr-xr-x 2 root    root    4096 Apr 23 18:04 .
drwxr-xr-x 3 root    root    4096 Apr 23 18:04 ..
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file00
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file01
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file02
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file03
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file04
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file05
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file06
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file07
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file08
-rw-r----- 1 bandit5 bandit4   33 Apr 23 18:04 -file09
```

Okay, all files have the exact same size, so we know that one file is contains the `flag`, the others are probably just decoys.

We can use `file` to check the type of the files. Using `file` with a wildcard `*` we can show the types/type of content
of the files.

``` commandline 
bandit4@bandit:~/inhere$ file ./-*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: Non-ISO extended-ASCII text, with no line terminators
```
Hmm, considering all files contain just `data` except file `./-file07`, which contains `ASCII text`, we can print out the
contents of `./-file07` to check if the flag is hidden in there.

``` commandline
bandit4@bandit:~/inhere$ cat ./-file07
lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR
```

The flag was hidden in file 7. We got a flag. 

