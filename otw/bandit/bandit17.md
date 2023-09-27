# Bandit 17

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

We can establish a connection with the server via ssh, and using the password `VwOSWtCA7lRKkTfbr2IDh6awj9RNZM5e`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit17@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit17@bandit.labs.overthewire.org's password: VwOSWtCA7lRKkTfbr2IDh6awj9RNZM5e


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

bandit17@bandit:~$ 
```

## Task

--------------

There are 2 files in the homedirectory: passwords.old and passwords.new.
The password for the next level is in passwords.new and is the only line that has been changed between passwords.old 
and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, 
bandit19

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
bandit17@bandit:~$ ls
passwords.new  passwords.old
bandit17@bandit:~$ pr -m -t passwords.new passwords.old | head
SEN5dn6xYkQItiimhyqAlmecmMwBtUxQ    SEN5dn6xYkQItiimhyqAlmecmMwBtUxQ
RJIavo1DXZXFjpv3c4BB22gJf6jkR3By    RJIavo1DXZXFjpv3c4BB22gJf6jkR3By
fRu6dfM5HFNjkRHSPpDqKHMuo4IXDISL    fRu6dfM5HFNjkRHSPpDqKHMuo4IXDISL
1ryPLzBZjmePEjUMGIGGx0IZoRTP5WfG    1ryPLzBZjmePEjUMGIGGx0IZoRTP5WfG
C1aTZwWUpZp3RiPhOezpyv8uI7XyRxNX    C1aTZwWUpZp3RiPhOezpyv8uI7XyRxNX
YwYnbb0AID81Q6gTjIuIlUF8E6CaHury    YwYnbb0AID81Q6gTjIuIlUF8E6CaHury
6crimLWCTIxTbEPuMcETzsXeR1E1dEiO    6crimLWCTIxTbEPuMcETzsXeR1E1dEiO
95tD0m3soHHPT6mjsdCBw4o03alLbbQe    95tD0m3soHHPT6mjsdCBw4o03alLbbQe
6gWKTBH7ENMeNpFIedCvTBNCgpM0EoIB    6gWKTBH7ENMeNpFIedCvTBNCgpM0EoIB
MurtzHjt9cWZ6qQvfDk7sXkBsfc9BloE    MurtzHjt9cWZ6qQvfDk7sXkBsfc9BloE
bandit17@bandit:~$ 
bandit17@bandit:~$ wc -l *
 100 passwords.new
 100 passwords.old
 200 total
bandit17@bandit:~$ 
```

**Note: The `pr` command is used for converting text files for printing*

The two files are filled with hashes. Apparently, `passwords.old` are old passwords and in the `passwords.new` file ONE
line was changed, which is the password for `bandit18`.

We can use the `diff` command for this task. `diff` compares files line by line.

``` text
bandit17@bandit:~$ diff passwords.new passwords.old 
42c42
< hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg
---
> glZreTEH1V3cGKL6g4conYqZqaEj0mte
```

`hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg` is the new hash in `password.new`.

``` text
bandit17@bandit:~$ cat passwords.new | grep "hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg"
hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg
```

Boom found a new flag.


