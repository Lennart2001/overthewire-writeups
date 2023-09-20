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

We can establish a connection with the server via ssh, and using the password `hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit18@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg


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
Byebye !
Connection to bandit.labs.overthewire.org closed. 
```

## Task

--------------

The password for the next level is stored in a file readme in the homedirectory.
Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

## Looking Around

--------------

Uhh... we cannot connect to the server. This is weird. Looking at the task, we are supposed to alter the `.bashrc` file.
Perhaps we can access bandit18 directory via the bandit17 directory.


``` text
bandit17@bandit:~$  cd /home/bandit18
bandit17@bandit:/home/bandit18$ ls
readme
bandit17@bandit:/home/bandit18$ cat readme
cat: readme: Permission denied
bandit17@bandit:/home/bandit18$ 
```

Alright. We cannot read the file, but we are able to view the file. Let's `list` the hidden files as well, as they are

``` text
bandit17@bandit:/home/bandit18$ ls -al
total 24
drwxr-xr-x  2 root     root     4096 Apr 23 18:04 .
drwxr-xr-x 70 root     root     4096 Apr 23 18:05 ..
-rw-r--r--  1 root     root      220 Jan  6  2022 .bash_logout
-rw-r-----  1 bandit19 bandit18 3794 Apr 23 18:04 .bashrc
-rw-r--r--  1 root     root      807 Jan  6  2022 .profile
-rw-r-----  1 bandit19 bandit18   33 Apr 23 18:04 readme
bandit17@bandit:/home/bandit18$ cat .bashrc
cat: .bashrc: Permission denied
```

As expected, we also cannot read this file, as the User and Group are bandit19 and bandit18, respectively.

What we do know is that we are able to connect and receive a response from the server: `Byebye !`. This means, we connect
and then some script gets run that kicks us off. However, in that instance we might be able to read some files from the terminal.


#### Command Execution

- `Without Command`: When you run `ssh user@server -p 2220` without specifying a command, SSH defaults to providing an interactive shell.
- `With Command`: When you append a command like `cat readme`, SSH executes that command on the remote system and returns the output. We do not get an interactive shell.

Let's test via `bandit1`:


``` text
➜  ~  ssh bandit1@bandit.labs.overthewire.org -p 2220 "cat ./-"   
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit1@bandit.labs.overthewire.org's password: 
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
➜  ~  
```

We only receive the output and  nothing more. We do not receive a interactive shell.


---------------


Now, since we know that we can use `ssh` to execute code without an interactive shell, we can simply pass the `cat readme` command
at the end of the ssh command and we should receive the flag. 


``` text
➜  ~  ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: 
awhqfNnAbc1naukrpqDYcF95h7HoMTrC
```


Boom we got another flag.



