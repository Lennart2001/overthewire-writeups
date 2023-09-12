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

We can establish a connection with the server via ssh, and using the password `NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit1@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit1@bandit.labs.overthewire.org's password: NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL


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

bandit1@bandit:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit1@bandit:~$ ls
-
```

Okay, so we have a file named "-" in the current working directory. We should be simply be able to print the contents
using `cat`:

``` commandline
bandit1@bandit:~$ cat -
hello
hello
sjjjj
sjjjj
```

Hmm, this is interesting. Apparently `cat` thinks that we are trying to specify a flag with the "-" symbol, and it just 
mirrors whatever we type. This is not great.

One way to solve this is by forcing cat to interpret the "-" as a file via the `./` prefix.

**The "." (dot) is a special directory name that refers to the current directory.
When we prefix a filename with "./", we're specifying that the file resides in the current directory.
Thereby forcing `cat` to read "-" as a file.**

``` commandline
bandit1@bandit:~$ cat ./-
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
```

We found the flag. However, let's test `cat ./` with a filename that doesn't exist in the directory:


``` commandline
bandit1@bandit:~$ cat ./test
cat: ./test: No such file or directory
```

Okay, great. We are getting an error. Let's try another test, by going up one directoyr (in the parent directory)
and try to read the "-" file from there:


``` commandline
bandit1@bandit:~$ cd ..
bandit1@bandit:/home$ ls
bandit0   bandit14  ... ... ...  krypton1  krypton7
bandit1   bandit15  ... ... ...  krypton2  ubuntu
bandit10  bandit16  ... ... ...  krypton3
bandit11  bandit17  ... ... ...  krypton4
bandit12  bandit18  ... ... ...  krypton5
bandit13  bandit19  ... ... ...  krypton6
bandit1@bandit:/home$ cat bandit1/-
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
```

As we can see, we are *forcing* cat to treat "-" as a filename instead of a prefix for a flag, when we are including a path to the file.
This is because flags do not have filepaths, therefore "-" gets treated as a file.

*Note: I actually found the flag via going up one directory and executing `cat` from there. 
