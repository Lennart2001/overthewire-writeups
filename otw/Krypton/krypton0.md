# Krypton 0


## Overview

-----------------
On the OverTheWire/Leviathan website, we are told how connecting to the bandit server as a specific user works.

Each user corresponds to a different level:
- Krypton0 -> Level 0
- Krypton1 -> Level 1
- ...
- Krypton33 -> Level 33

Each level is hiding a flag, which is used as the password for the following level. Meaning, one must solve `Level n` to play `Level n+1`.

We always start out connecting to the `krypton.labs.overthewire.org` server on `port 2231` for each level.
Again, the users on the server specifies the level. Each starting point is the same:

``` text
➜  ~  ssh krypton1@leviathan.labs.overthewire.org -p 2231          
                   _            _       _   _                        
                  | | _____   _(_) __ _| |_| |__   __ _ _ __         
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \        
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |       
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|       
                                                                     
                                                                     
                      This is an OverTheWire game server.            
            More information on http://www.overthewire.org/wargames  
                                                                     
krypton1@leviathan.labs.overthewire.org's password:     
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `leviathan0`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `KRYPTONISGREAT`:

``` text
➜  ~  ssh krypton1@krypton.labs.overthewire.org -p 2231
                      _                     _              
                     | | ___ __ _   _ _ __ | |_ ___  _ __  
                     | |/ / '__| | | | '_ \| __/ _ \| '_ \ 
                     |   <| |  | |_| | |_) | || (_) | | | |
                     |_|\_\_|   \__, | .__/ \__\___/|_| |_|
                                |___/|_|                   

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

krypton1@krypton.labs.overthewire.org's password: KRYPTONISGREAT

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

  Enjoy your stay!

krypton1@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
krypton1@bandit:~$ cd /krypton
krypton1@bandit:/krypton$ ls
krypton1  krypton2  krypton3  krypton4  krypton5  krypton6
krypton1@bandit:/krypton$ cd krypton1
krypton1@bandit:/krypton/krypton1$ ls
krypton2  README
krypton1@bandit:/krypton/krypton1$ cat README 
Welcome to Krypton!

This game is intended to give hands on experience with cryptography
and cryptanalysis.  The levels progress from classic ciphers, to modern,
easy to harder.

Although there are excellent public tools, like cryptool,to perform
the simple analysis, we strongly encourage you to try and do these
without them for now.  We will use them in later excercises.

** Please try these levels without cryptool first **


The first level is easy.  The password for level 2 is in the file 
'krypton2'.  It is 'encrypted' using a simple rotation called ROT13.  
It is also in non-standard ciphertext format.  When using alpha characters for
cipher text it is normal to group the letters into 5 letter clusters, 
regardless of word boundaries.  This helps obfuscate any patterns.

This file has kept the plain text word boundaries and carried them to
the cipher text.

Enjoy!
```

Alrighty. So Krypton is all just ciphers and encryption stuff. I like it.

Let's dive right in. We are told that the text in `krypton2` was encoded using the ROT13 algorithm. Which means that each
letter was moved 13 places forward in the alphabet. We can use the `tr` command to do decode it. 

Keep in mind that `tr` doesn't do any wild encoding,  it just replaces letters with other letters based on the pattern
we give it. The `N-Z` is just a shortcut for `NOPQRSTUVWXYZ`. So the total string would be `NOPQRSTUVWXYZABCDEFGHIJKLM`.


``` text
krypton1@bandit:/krypton/krypton1$ cat krypton2 
YRIRY GJB CNFFJBEQ EBGGRA
krypton1@bandit:/krypton/krypton1$ cat krypton2 | tr "N-ZA-Mn-za-m" "A-Za-z"
LEVEL TWO PASSWORD ROTTEN
krypton1@bandit:/krypton/krypton1$ cat krypton2 | tr "NOPQRSTUVWXYZABCDEFGHIJKLM" "ABCDEFGHIJKLMNOPQRSTEVWXYZ"
LEVEL TWO PASSWORD ROTTEN
```

BOOM we got the password for the next level.
