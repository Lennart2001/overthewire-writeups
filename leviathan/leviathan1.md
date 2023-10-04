# Leviathan 1


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

We can establish a connection with the server via ssh, and using the password `PPIfmI1qsA`:

``` text
➜  ~  ssh leviathan1@leviathan.labs.overthewire.org -p 2223
                   _            _       _   _                 
                  | | _____   _(_) __ _| |_| |__   __ _ _ __  
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \ 
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|
                                                              

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

leviathan1@leviathan.labs.overthewire.org's password: PPIfmI1qsA

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

leviathan1@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
leviathan1@gibson:~$ ls -la
total 36
drwxr-xr-x  2 root       root        4096 Apr 23 18:04 .
drwxr-xr-x 83 root       root        4096 Apr 23 18:06 ..
-rw-r--r--  1 root       root         220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root       root        3771 Jan  6  2022 .bashrc
-r-sr-x---  1 leviathan2 leviathan1 15072 Apr 23 18:04 check
-rw-r--r--  1 root       root         807 Jan  6  2022 .profile
leviathan1@gibson:~$
```

Let's check out what the `check` file does.

``` text
leviathan1@gibson:~$ ./check
password: hello
Wrong password, Good Bye ...
leviathan1@gibson:~$ ./check
password: world
Wrong password, Good Bye ...
leviathan1@gibson:~$ ./check
password: password
Wrong password, Good Bye ...
leviathan1@gibson:~$ 
```

Hmm. This seems pretty hard.

Let's take a look inside the file and see if we can guess anything.

``` text
leviathan1@gibson:~$ head check
ELF4X64 
        (444``????? ?
                    /
                     ?
                     ,0/?????DDP?td8 88,,Q?tdR?td
                                                 /
                                                  ?
                                                  ??/lib/ld-linux.so.2GNU??	??9@?Q?h??]???GNU
                                                                                                   
                                                                                                   ?K??N8?"U3G? ]_IO_stdin_used__libc_start_main__stack_chk_failputsprintfgetchars????ziircmpgeteuidsetreuidlibc.so.6GLIBC_2.4GLIBC_2.34GLIBC_2.0__gmon_start__fii
         $,?
??S?????/????????t?Ѓ[??5??%
                           h??????%??????%h??????%h?????%h ?????% h(?????%$h0?????%(h8?p????%,h@?`?????1?^????PTR???/jjQV??P?T????$?f?f?f?f?f?f?f?????f?f?f?f?f???$?f?f?f?f?f?f??8=8t$???t???h8?Ѓ??Í?&f?Í?&??&??8-8?????????t ???tU???Ph8?҃??Ít&Í?&???=8u???h????8?Í?&Í?&??늍L$????q?U??SQ?? e??E?1??E?sex?E?secrf?E?et?E??E?god?E?love?E???
                                                                                                                                                         ?&??????.????E??&????E??????E??E???E?P?E?P?????????u+??????????SP?:???????
                                                 h?????????
                                                           ?????????U?e+t?????e?Y[]?a????S??G?????'-?[?password: /bin/shWrong password, Good Bye ...(????l????D????X?????zR|
                                                                                                                                                                           L???10x??? D????F
          J
           tx?;*2$"0h????D
                          IuBuxu|??
                                   A?A?C
                                        ??f
̒                                           ?
???ô?
   ?
?
?8T{????o???ojFVfv????GCC: (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0 ??0?
/8;?CU8b}?0??? ?4???<??
   crt1.o__abi_tagcrtstuff.cderegister_tm_clones__do_global_dtors_auxcompleted.0__do_global_dtors_aux_fini_array_entryframe_dummy__frame_dummy_init_array_entrycheck.c__FRAME_END___DYNAMIC__GNU_EH_FRAME_HDR_GLOBAL_OFFSET_TABLE_strcmp@GLIBC_2.0__libc_start_main@GLIBC_2.34__x86.get_pc_thunk.bxprintf@GLIBC_2.0getchar@GLIBC_2.0_edata_fini__stack_chk_fail@GLIBC_2.4geteuid@GLIBC_2.0__data_startputs@GLIBC_2.0system@GLIBC_2.0__gmon_start____dso_handle_IO_stdin_usedsetreuid@GLIBC_2.0_end_dl_relocate_static_pie_fp_hw__bss_startmain__TMC_END___init.symtab.strtab.shstrtab.interp.note.gnu.build-id.note.ABI-tag.gnu.hash.dynsym.dynstr.gnu.version.gnu.version_r.rel.dyn.rel.plt.init.text.fini.rodata.eh_frame_hdr.eh_frame.init_array.fini_array.dynamic.got.got.plt.data.bss.comment?#??$? D???o? N

                                                                          ?
                                                                          ???^???ojjk???o??@z  ??       ??$?00?????? 8?88 ,?dd ??
                                                                                                                                 ?
                                                                                                                                 /?/?/???/?00?00880?080+d0?	3AU5leviathan1@gibson:~$ 

```

Alright. We can kind of see familiar things in the text. For instance, `password: /bin/shWrong password, Good Bye ...`. 
This tells us that some things are kept in `plain-text` for printing. Perhaps they left some passwords in plain text
for comparison. After a bit of looking I found this: `e??E?1??E?sex?E?secrf?E?et?E??E?god?E?love?E???`

Now, what stood out to me where the seemingly random words in that string: `sex, secrf, god, love`. I mean, what does it
mean? 

I thought, doesn't hurt to try those words.


``` text
leviathan1@gibson:~$ ./check
password: sex
$ ls
check
$ whoami       
leviathan2
$ cat /etc/leviathan_pass/leviathan2
mEh5PNl10e
```

And I was given a shell after entering `sex` and I checked `whoami` and I was `leviathan2` which allowed me to view the
password for `leviathan2`. So that's what I did.

I found the password. 


## NOT LUCK ANSWER

Okay, after looking up the answer, we found that people used the `ltrace` command. 

``` text
leviathan1@gibson:~$ ltrace ./check
__libc_start_main(0x80491e6, 1, 0xffffd5e4, 0 <unfinished ...>
printf("password: ")                                                                                          = 10
getchar(0xf7fbe4a0, 0xf7fd6f80, 0x786573, 0x646f67password: helllo
)                                                           = 104
getchar(0xf7fbe4a0, 0xf7fd6f68, 0x786573, 0x646f67)                                                           = 101
getchar(0xf7fbe4a0, 0xf7fd6568, 0x786573, 0x646f67)                                                           = 108
strcmp("hel", "sex")                                                                                          = -1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                                                                          = 29
+++ exited (status 0) +++
```

This gives us a look behind the curtain and shows us that the program takes the first three characters of the input and
compares it against the string `sex`. 

Here is what `ltrace` does.

``` text
leviathan1@gibson:~$ man ltrace
DESCRIPTION
       ltrace is a program that simply runs the specified command until it exits.  It intercepts and records the dynamic library calls  which  are  called  by  the  executed
       process and the signals which are received by that process.  It can also intercept and print the system calls executed by the program.

       Its use is very similar to strace(1).
```


So, while we got the right answer. It was more luck than anything else. The `ltrace` command is very useful for this type
of debugging.

