# Bandit 5

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

We can establish a connection with the server via ssh, and using the password `lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit5@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit5@bandit.labs.overthewire.org's password: lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR


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

bandit5@bandit:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit5@bandit:~$ ls
inhere
```

``` commandline
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ l
maybehere00/  maybehere02/  ... ...  maybehere18/
maybehere01/  maybehere03/  ... ...  maybehere19/
```

We were given specific features of the flag file. The file in which the flag is, is non-executable, human-readable, and is 1033 bytes in size.
We can use the `find` command to go through each directory recursively and find the file with the specified features.

Using the `man pages` for `find` we can find this:

``` commandline
<snip>
-readable
       Matches files which are readable by the current user.  This takes into account access control  lists  and  other  permissions
       artefacts  which  the  -perm  test  ignores.   This  test makes use of the access(2) system call, and so can be fooled by NFS```
       servers which do UID mapping (or root-squashing), since many systems implement access(2) in the client's kernel and so cannot
       make use of the UID mapping information held on the server.   

<snip>

-size n[cwbkMG]
              File uses less than, more than or exactly n units of space, rounding up.  The following suffixes can be used:

              `b'    for 512-byte blocks (this is the default if no suffix is used)

              `c'    for bytes

              `w'    for two-byte words

              `k'    for kibibytes (KiB, units of 1024 bytes)

              `M'    for mebibytes (MiB, units of 1024 * 1024 = 1048576 bytes)

              `G'    for gibibytes (GiB, units of 1024 * 1024 * 1024 = 1073741824 bytes)

              The size is simply the st_size member of the struct stat populated by the lstat (or stat) system call, rounded  up  as  shown
              above.  In other words, it's consistent with the result you get for ls -l.  Bear in mind that the `%k' and `%b' format speci‐
              fiers of -printf handle sparse files differently.  The `b' suffix always denotes 512-byte blocks and never 1024-byte  blocks,
              which is different to the behaviour of -ls.

              The  +  and  - prefixes signify greater than and less than, as usual; i.e., an exact size of n units does not match.  Bear in
              mind that the size is rounded up to the next unit.  Therefore -size -1M is not equivalent  to  -size -1048576c.   The  former
              only matches empty files, the latter matches files from 0 to 1,048,575 bytes.
<snip>
```

Now, using the flag `-readable` and `-size n[cwbkMG]` we can try to find a file that is `1033 bytes` and is `readable`.

``` commandline
bandit5@bandit:~/inhere$ find -size 1033c -readable
./maybehere07/.file2
```

And look at that, we found one file. Let's print the contents.

``` commandline 
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU




bandit5@bandit:~/inhere$ 
```

Boom. We got another flag.


