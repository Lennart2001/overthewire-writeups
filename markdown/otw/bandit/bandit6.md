# Bandit 6

### Content
- **[Overview](#Overview)**
- **[Task](#Task)**
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

## Task

--------------
``` commandline
The password for the next level is stored somewhere on the server and has all of the following properties:

owned by user bandit7
owned by group bandit6
33 bytes in size
```


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit6@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit6@bandit.labs.overthewire.org's password: P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU


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

bandit6@bandit:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit6@bandit:~$ ls
bandit6@bandit:~$
```

Okay, nothing gets shown. Let's try adding some flags.

``` commandline
bandit6@bandit:~$ ls -la
total 20
drwxr-xr-x  2 root root 4096 Apr 23 18:04 .
drwxr-xr-x 70 root root 4096 Apr 23 18:05 ..
-rw-r--r--  1 root root  220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root root 3771 Jan  6  2022 .bashrc
-rw-r--r--  1 root root  807 Jan  6  2022 .profile
```

Okay, also no hidden files. Given what we were told from the "Task" field, the creators of this game actually hid the
file somewhere on the server. Not specifically in the `bandit6` directory.

This complicates things and makes it more challenging to find the flag. We can use the `find` command with a couple cool flags
to try to narrow down the search.

We will have to use multiple pipelines to find the file, so we will break down each command and their functions.

``` commandline
bandit6@bandit:~$ find / -user bandit7 -group bandit6
find: ‘/var/log’: Permission denied
find: ‘/var/crash’: Permission denied
find: ‘/var/spool/rsyslog’: Permission denied
find: ‘/var/spool/bandit24’: Permission denied
find: ‘/var/spool/cron/crontabs’: Permission denied
find: ‘/var/tmp’: Permission denied
...
...
find: ‘/root’: Permission denied
find: ‘/sys/kernel/tracing’: Permission denied
find: ‘/sys/kernel/debug’: Permission denied
find: ‘/sys/fs/pstore’: Permission denied
find: ‘/sys/fs/bpf’: Permission denied
bandit6@bandit:~$ 
```

When we use `find /` we are starting out in the root directory. The root directory is the highest point in the file system.
There is nothing higher than the root directory and every file can be found somewhere, if one propagates through the correct directories,
starting with the root directory.

- `find /` We are starting out search in the root directory
- `-user bandit7` We specify that the user of the file/directory must be `bandit7`
- `-group bandit6` We specify that the group of the file/directory must be `bandit6`

``` commandline
grep --fixed-strings --invert-match --ignore-case "permission"
```
`grep` allows us to print lines that match a certain pattern. We can add flags to specify what kind of lines are being printed.
For instance, in our example we used `--fixed-strings`, `--invert-match` and `--ignore-case`.

- `--fixed-strings` Interpret PATTERNS as fixed strings, not regular expressions (regex).
- `--invert-match` Invert the sense of matching, to select non-matching lines.
- `--ignore-case` Ignore case distinctions in patterns and input data, so that characters that differ only in case match each other.


``` commandline
grep --fixed-strings --invert-match --ignore-case "directory"
```

