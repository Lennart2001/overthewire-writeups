# Bandit 6

### Content
- **[Overview](#Overview)**
- **[Task](#Task)**
- **[Connecting](#Connecting)**
- **[Looking Around](#Looking-Around)**
- **[Crafting a Command](#Crafting-a-Command)**
  - [Workflow of a Pipeline](#Workflow-of-a-Pipeline)


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

## Crafting a Command     

We will have to use multiple pipelines to find the file, so we will break down each command and their functions.

``` commandline
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>&1
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
- `-size 33c` We specify the size of the file/directory must be 33 bytes
- `2>&1` We specify that the `stderr` gets added to `stdout`

#### 2>&1

What is being printed in the terminal and what would be sent through to the next command are not the same thing.
In Linux and other Unix-like operating systems, programs have access to three standard streams:

- **Standard Input** (`stdin`) - File Descriptor 0
- **Standard Output** (`stdout`) - File Descriptor 1
- **Standard Error** (`stderr`) - File Descriptor 2
                  
The terminal combines the `stdout` and `stderr` but only passes on `stdout` to files or outputs (by default).
We can specify that we *append* or *add* the `stderr` to the `stdout` via the *File Descriptors*.


- `2>` Redirects the standard error (`stderr`), which has the file descriptor 2.                                                             
- `&1` The ampersand `&` indicates that what follows is a file descriptor, not a filename. The 1 refers to standard output (`stdout`), which has the file descriptor 1.


By using `2>&1` in the find command, we make sure that any error messages (like "Permission denied") 
are also sent through the pipeline, allowing subsequent grep commands to filter them out if necessary.


-----------




``` commandline
grep --fixed-string --invert-match --ignore-case "permission"
```
`grep` allows us to print lines that match a certain pattern. We can add flags to specify what kind of lines are being printed.
For instance, in our example we used `--fixed-strings`, `--invert-match` and `--ignore-case`.

- `--fixed-strings` Interpret PATTERNS as fixed strings, not regular expressions (regex).
- `--invert-match` Invert the sense of matching, to select non-matching lines.
- `--ignore-case` Ignore case distinctions in patterns and input data, so that characters that differ only in case match each other.

We specified to ignore lines that contain the word "permission" (ignoring the case of individual characters) with this command.

``` commandline
grep --fixed-string --invert-match --ignore-case "directory"
```

We specified to ignore lines that contain the word "directory" (ignoring the case of individual characters) with this command.

We now are able to combine all three commands via pipelines (`|`). 
Pipelines in a Unix-like operating system such as Linux allow you to chain multiple commands together. 
The standard output (`stdout`) of one command becomes the standard input (`stdin`) of the next command. 
                    
#### Workflow of a Pipeline

``` bash
command1 | command2 | command3 | ...
```

- **Initialization**: When the shell encounters the pipe symbol (`|`), it creates a pipe — an inter-process communication mechanism.
- **Forking**: The shell then forks itself to create a child process for each command in the pipeline.
- **Execution**: Each command in the pipeline is executed in its own child process.
- **Data Flow**: The `stdout` of `command1` is connected to the `stdin` of `command2`, and so on. This allows data to flow seamlessly through the pipeline.
- **Synchronization**: Each command waits for its predecessor to provide input (if any) and for its successor to read its output (if any).
---------------

Now, we can create a multi-pipeline command.

``` commandline
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>&1 | grep --fixed-string --invert-match --ignore-case "permission" | grep --fixed-string --invert-match --ignore-case "directory"
/var/lib/dpkg/info/bandit7.password
```

We are given one filepath. Lets print the file.

``` commandline
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
```

Boom we got the flag. 
      

