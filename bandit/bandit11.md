# Bandit 11

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

We can establish a connection with the server via ssh, and using the password `6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit11@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit11@bandit.labs.overthewire.org's password: 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM


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

bandit11@bandit:~$ 
```

## Task

--------------

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit11@bandit:~$ ls
data.txt
```

Okay we have another `data.txt` file. Let's check out the first couple lines via `head` command.

``` commandline
bandit11@bandit:~$ head data.txt 
Gur cnffjbeq vf WIAOOSFzMjXXBC0KoSKBbJ8puQm5lIEi
```

Okay. This is interesting. It seems like the contents have been shifted by a certain amount. Perhaps a ROT13 algorithm.

Let's test it out with the `tr` command. 

``` commandline
bandit11@bandit:~$ cat data.txt | tr "N-ZA-Mn-za-m" "A-Za-z"
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

- If it's an uppercase letter from `N to Z`, it's mapped to `A to M`.
- If it's an uppercase letter from `A to M`, it's mapped to `N to Z`.
- If it's a lowercase letter from `n to z`, it's mapped to `a to m`.
- If it's a lowercase letter from `a to m`, it's mapped to `n to z`.

The first set is `N-ZA-Mn-za-m`, which could be expanded to `NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm`.

The second set is `A-Za-z`, which expands to `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz`.

``` text
NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
```

An equivalent command would be this: 

``` commandline
bandit11@bandit:~$ cat data.txt | tr "NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm" "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

- `N-Z` is shorthand for `NOPQRSTUVWXYZ`
- `A-M` is shorthand for `ABCDEFGHIJKLM`
- `A-Z` is shorthand for `ABCDEFGHIJKLMNOPQRSTUVWXYZ`

Since we are just "replacing" letters we could also write this:

``` commandlind
bandit11@bandit:~$ cat data.txt | tr "N-ZA-MN-ZA-MN-ZA-Mn-za-m" "A-ZA-ZA-Za-z"
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

Boom we found a flag.


