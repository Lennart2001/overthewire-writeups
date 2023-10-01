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

We can establish a connection with the server via ssh, and using the password `YZ55XPVk2l`:

``` text
➜  ~  ssh leviathan6@leviathan.labs.overthewire.org -p 2223
                   _            _       _   _                 
                  | | _____   _(_) __ _| |_| |__   __ _ _ __  
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \ 
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|
                                                              

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

leviathan6@leviathan.labs.overthewire.org's password: YZ55XPVk2l

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

leviathan6@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
leviathan6@gibson:~$ ls
leviathan6
leviathan6@gibson:~$ ./leviathan6 
usage: ./leviathan6 <4 digit code>
leviathan6@gibson:~$ ./leviathan6 0231
Wrong
leviathan6@gibson:~$
```

Hmm. Interesting. So we need to pass a 4-digit number when calling the file. This is some sort of password.

We could simply create a python script or bash script and brute force the password. But the rules tell us that we don't 
need to code anything. We simply need to analyze the code and think a bit outside the box to crack the problem. So let's
crack on.


``` text
leviathan6@gibson:~$ ltrace ./leviathan6 9999
__libc_start_main(0x80491d6, 2, 0xffffd5e4, 0 <unfinished ...>
atoi(0xffffd73c, 0xf7fd6f80, 0xf7c184be, 0xf7fbe4a0)                                    = 9999
puts("Wrong"Wrong
)                                                                           = 6
+++ exited (status 0) +++
```

Alright we have the `atoi()` system call, which converts a string to a number.

``` text
#! /bin/bash

for i in {0000..9999}; do
	output=$(~/leviathan6 $i)
	echo $i
	if [[ "$output" != "Wrong" ]]; then
		echo "THE SOLUTION IS^^^^^"
		break
	fi
done
```
OR 
`for i in {0000..9999}; do ./leviathan6 $i; done` As a one-liner.


``` text
<snip>
7115
7116
7117
7118
7119
7120
7121
7122
ls
ls: cannot open directory '.': Permission denied
whoami

```

Hmm... This is very suspicious. Almost like we got a shell. But our bash script doesn't support shells. What if we type
`exit`? Will we exit the shell?

``` text
7120
7121
7122
ls
ls: cannot open directory '.': Permission denied
whoami
exit
7123
THE SOLUTION IS^^^^^
```

Yup we did exit the shell. Alright lets use `7123` as out input for the `./leviathan6`script.

``` text
leviathan6@gibson:~$ ./leviathan6 7123
$ whoami
leviathan7
$ cat /etc/leviathan_pass/leviathan7
8GpZ5f8Hze
$ 
```

BOOOM We got a shell AND THE FLAG!!! Scripting for the win!!


## Better Way to Solve

Now, imagine it wasn't a four-digit passcode, but instead a 16-character long printable ASCII text password. How many possible 
combinations are there?

``` text
95^16 = 44,012,666,865,176,569,775,543,212,890,625
```

So... our previous brute force approach worked for us, but is not optimal. Additionally, if each wrong password entry 
calls a function that pauses the program for `1-2 seconds` it would take forever to crack the code, even if it is just 
10000 possible combinations.

#### How to do better?

Well, after looking online, I found that using the `gdb` command, allows us to look through the assembly language code.

``` text
NAME
       gdb - The GNU Debugger

SYNOPSIS
       gdb [OPTIONS] [prog|prog procID|prog core]

DESCRIPTION
       The purpose of a debugger such as GDB is to allow you to see what is going on "inside" another program while it executes -- or what
       another program was doing at the moment it crashed.

       GDB can do four main kinds of things (plus other things in support of these) to help you catch bugs in the act:

       •   Start your program, specifying anything that might affect its behavior.

       •   Make your program stop on specified conditions.

       •   Examine what has happened, when your program has stopped.

       •   Change things in your program, so you can experiment with correcting the effects of one bug and go on to learn about another.

       You can use GDB to debug programs written in C, C++, Fortran and Modula-2.

       GDB is invoked with the shell command "gdb".  Once started, it reads commands from the terminal until you tell it to exit with the
       GDB command "quit" or "exit".  You can get online help from GDB itself by using the command "help".

       You can run "gdb" with no arguments or options; but the most usual way to start GDB is with one argument or two, specifying an
       executable program as the argument:

               gdb program

       You can also start with both an executable program and a core file specified:

               gdb program core

<snip>

       --args prog [arglist]
           Change interpretation of command line so that arguments following this option are passed as arguments to the inferior.  As an
           example, take the following command:

                   gdb ./a.out -q

           It would start GDB with -q, not printing the introductory message.  On the other hand, using:

                   gdb --args ./a.out -q

           starts GDB with the introductory message, and passes the option to the inferior.

<snip>
```

Okay, so we can use the `gdb` command with the `--args` flag to pass in a parameter to the `./leviathan6` file.


``` text
leviathan6@gibson:~$ gdb --args ./leviathan6 0000
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./leviathan6...
(No debugging symbols found in ./leviathan6)
(gdb) disassemble main
```

Now, we disassemble the `main()` function of the program. 

``` text
Dump of assembler code for function main:
   0x080491d6 <+0>:	lea    0x4(%esp),%ecx
   0x080491da <+4>:	and    $0xfffffff0,%esp
   0x080491dd <+7>:	push   -0x4(%ecx)
   0x080491e0 <+10>:	push   %ebp
   0x080491e1 <+11>:	mov    %esp,%ebp
   0x080491e3 <+13>:	push   %ebx
   0x080491e4 <+14>:	push   %ecx
   0x080491e5 <+15>:	sub    $0x10,%esp
   0x080491e8 <+18>:	mov    %ecx,%eax
   0x080491ea <+20>:	movl   $0x1bd3,-0xc(%ebp)
   0x080491f1 <+27>:	cmpl   $0x2,(%eax)
   0x080491f4 <+30>:	je     0x8049216 <main+64>
   0x080491f6 <+32>:	mov    0x4(%eax),%eax
   0x080491f9 <+35>:	mov    (%eax),%eax
   0x080491fb <+37>:	sub    $0x8,%esp
   0x080491fe <+40>:	push   %eax
   0x080491ff <+41>:	push   $0x804a008
   0x08049204 <+46>:	call   0x8049050 <printf@plt>
   0x08049209 <+51>:	add    $0x10,%esp
   0x0804920c <+54>:	sub    $0xc,%esp
   0x0804920f <+57>:	push   $0xffffffff
   0x08049211 <+59>:	call   0x8049090 <exit@plt>
   0x08049216 <+64>:	mov    0x4(%eax),%eax
   0x08049219 <+67>:	add    $0x4,%eax
   0x0804921c <+70>:	mov    (%eax),%eax
   0x0804921e <+72>:	sub    $0xc,%esp
   0x08049221 <+75>:	push   %eax
   0x08049222 <+76>:	call   0x80490b0 <atoi@plt>
   0x08049227 <+81>:	add    $0x10,%esp
   0x0804922a <+84>:	cmp    %eax,-0xc(%ebp)
   0x0804922d <+87>:	jne    0x804925a <main+132>
   0x0804922f <+89>:	call   0x8049060 <geteuid@plt>
   0x08049234 <+94>:	mov    %eax,%ebx
   0x08049236 <+96>:	call   0x8049060 <geteuid@plt>
   0x0804923b <+101>:	sub    $0x8,%esp
   0x0804923e <+104>:	push   %ebx
   0x0804923f <+105>:	push   %eax
   0x08049240 <+106>:	call   0x80490a0 <setreuid@plt>
   0x08049245 <+111>:	add    $0x10,%esp
   0x08049248 <+114>:	sub    $0xc,%esp
   0x0804924b <+117>:	push   $0x804a022
   0x08049250 <+122>:	call   0x8049080 <system@plt>
   0x08049255 <+127>:	add    $0x10,%esp
   0x08049258 <+130>:	jmp    0x804926a <main+148>
   0x0804925a <+132>:	sub    $0xc,%esp
--Type <RET> for more, q to quit, c to continue without paging--
   0x0804925d <+135>:	push   $0x804a02a
   0x08049262 <+140>:	call   0x8049070 <puts@plt>
   0x08049267 <+145>:	add    $0x10,%esp
   0x0804926a <+148>:	mov    $0x0,%eax
   0x0804926f <+153>:	lea    -0x8(%ebp),%esp
   0x08049272 <+156>:	pop    %ecx
   0x08049273 <+157>:	pop    %ebx
   0x08049274 <+158>:	pop    %ebp
   0x08049275 <+159>:	lea    -0x4(%ecx),%esp
   0x08049278 <+162>:	ret    
End of assembler dump.
(gdb) 
```

Alright we have printed out the whole main function in assembly using the `gdb` disassembler function. 

After going through the different assembly commands, we can see that there is only one `cmp` command and it is also
right after we call the `atoi()` function (which converts a string to an integer). So, there is a good chance that we
are going to compare the outcome of the `atoi()` function with the actual number.

We can set a breakpoint at the `0x0804922a <+84>:	cmp    %eax,-0xc(%ebp)` location, to check what the indivudal register
values are. Especially the `%ebp` register. 

Additionally, `-0xc(%ebp)` is called "base plus displacement" addressing, where we use `%ebp` as the base and displace it
by negative `0xc`, which is 12. So, let's find the value of `%ebp`.


``` text
(gdb) break *0x0804922a
Breakpoint 1 at 0x804922a
(gdb) run
Starting program: /home/leviathan6/leviathan6 0000
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x0804922a in main ()
(gdb) info registers
eax            0x0                 0
ecx            0x0                 0
edx            0xffffd71d          -10467
ebx            0xf7e2a000          -136142848
esp            0xffffd4c0          0xffffd4c0
ebp            0xffffd4d8          0xffffd4d8
esi            0xffffd5a4          -10844
edi            0xf7ffcb80          -134231168
eip            0x804922a           0x804922a <main+84>
eflags         0x286               [ PF SF IF ]
cs             0x23                35
ss             0x2b                43
ds             0x2b                43
es             0x2b                43
fs             0x0                 0
gs             0x63                99
k0             0x0                 0
k1             0x0                 0
k2             0x0                 0
k3             0x0                 0
k4             0x0                 0
k5             0x0                 0
k6             0x0                 0
k7             0x0                 0
(gdb)
```

Now that we have the *frozen* register values, we can start doing some math to figure out the actual value we are comparing
our input number against.

As established above, the program uses a "base plus displacement" addressing scheme. This means, we can *artificially* 
create that displacement and find out the value.

``` text
(gdb) print $ebp-0xc
$1 = (void *) 0xffffd4cc
```

The `%ebp` register typically holds a pointer to the current stack frame, and subtracting `0xc` by it, will most likely
give us the address of a local variable. 

``` text
(gdb) x 0xffffd4cc
0xffffd4cc:	0x00001bd3
```

The `x` command prints out the value of the memory address in hex form. 

``` text
(gdb) print/d 0x00001bd3
$2 = 7123
(gdb) 
```

We now translate the `0x00001bd3` to an integer using the `print/d` command. And the result is `7123`. 

We can now go back to the `bash` terminal and check out if we got the right code.

``` text
leviathan6@gibson:~$ ./leviathan6 7123
$ whoami
leviathan7
$ cat /etc/leviathan_pass/leviathan7
8GpZ5f8Hze
$ 
```

BOOM. We got the shell. We are `leviathan7`. And we printed out the flag. HELL YEA

Let's check out the last level. *Logging in same as before*

``` text
leviathan7@gibson:~$ ls
CONGRATULATIONS
leviathan7@gibson:~$ file CONGRATULATIONS 
CONGRATULATIONS: ASCII text
leviathan7@gibson:~$ cat CONGRATULATIONS 
Well Done, you seem to have used a *nix system before, now try something more serious.
(Please don't post writeups, solutions or spoilers about the games on the web. Thank you!)
leviathan7@gibson:~$ 
```

I can't read, so I have no idea what the last sentence says. 

