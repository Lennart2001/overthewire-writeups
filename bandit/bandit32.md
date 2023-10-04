# Bandit 32

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

We can establish a connection with the server via ssh, and using the password `rmCBvG56y58BXzv98yZGdO7ATVL5dW8y`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit32@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit32@bandit.labs.overthewire.org's password: rmCBvG56y58BXzv98yZGdO7ATVL5dW8y


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

WELCOME TO THE UPPERCASE SHELL
>>
```

## Task

--------------

After all this git stuff its time for another escape. Good luck!

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
>> ls
sh: 1: LS: Permission denied
>> vim
sh: 1: VIM: Permission denied
>> v
sh: 1: V: Permission denied
```

Okay. This shell sucks. Let's go back one level an try to figure out what's going on in the current level.

``` text
bandit31@bandit:~$ cat /etc/passwd | grep "bandit32"
bandit32:x:11032:11032:bandit level 32:/home/bandit32:/home/bandit32/uppershell
bandit31@bandit:~$ ls /home/bandit32
uppershell
```

Alright so we are running a shell named `uppershell`, which turns everything we type into uppercase letters.

After playing around for a while, we find that special characters are not changed.

``` text
>> *
sh: 1: uppershell: Permission denied
>> #
>> 0
sh: 1: 0: Permission denied
>> ~
sh: 1: /home/bandit32: Permission denied
```

So what if we used the `$0` command to exit the shell. The `$0` command can have multiple meanings, but generally speaking
it will either return the shell's name or the script that's being run. *In some cases it can also be a symlink, other program
or a custom response*. 

``` text
>> $0
$ ls
uppershell
$ pwd
/home/bandit32
$ echo $0
sh
$
```

In our case, the `$0` returns the shell, namely `uppercase` which invokes a new shell, where if run `echo $0` we get `sh`.

``` text
$ whoami
bandit33
$
```
WOW. We are `bandit33` for some reason. This mean's we can read the `/etc/bandit_pass/bandit33` file. Let's print the 
contents of `/etc/bandit_pass/bandit33`.

``` text
$ cat bandit33
odHo63fHiFqcWWJG9rLiLDtPm45KzUKy
```

BOOM we got the final flag.





