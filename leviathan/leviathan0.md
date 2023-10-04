# Leviathan 0


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

We can establish a connection with the server via ssh, and using the password `leviathan0`:

``` text
➜  ~  ssh leviathan0@leviathan.labs.overthewire.org -p 2223
                   _            _       _   _                 
                  | | _____   _(_) __ _| |_| |__   __ _ _ __  
                  | |/ _ \ \ / / |/ _` | __| '_ \ / _` | '_ \ 
                  | |  __/\ V /| | (_| | |_| | | | (_| | | | |
                  |_|\___| \_/ |_|\__,_|\__|_| |_|\__,_|_| |_|
                                                              

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

leviathan0@leviathan.labs.overthewire.org's password: leviathan0

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

leviathan0@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
leviathan0@gibson:~$ ls -la
total 24
drwxr-xr-x  3 root       root       4096 Apr 23 18:04 .
drwxr-xr-x 83 root       root       4096 Apr 23 18:06 ..
drwxr-x---  2 leviathan1 leviathan0 4096 Apr 23 18:04 .backup
-rw-r--r--  1 root       root        220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root       root       3771 Jan  6  2022 .bashrc
-rw-r--r--  1 root       root        807 Jan  6  2022 .profile
leviathan0@gibson:~$
```

Let's check out the `.backup` directory.

``` text
eviathan0@gibson:~/.backup$ ls
bookmarks.html
```

Okay. Let's cautiously check the size of `bookmarks.html` in case its a giant file. 

``` text
leviathan0@gibson:~/.backup$ wc -l bookmarks.html 
1399 bookmarks.html
leviathan0@gibson:~/.backup$ head bookmarks.html 
<!DOCTYPE NETSCAPE-Bookmark-file-1>
<!-- This is an automatically generated file.
     It will be read and overwritten.
     DO NOT EDIT! -->
<META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=UTF-8">
<TITLE>Bookmarks</TITLE>
<H1 LAST_MODIFIED="1160271046">Bookmarks</H1>

<DL><p>
    <DT><H3 LAST_MODIFIED="1160249304" PERSONAL_TOOLBAR_FOLDER="true" ID="rdf:#$FvPhC3">Bookmarks Toolbar Folder</H3>
leviathan0@gibson:~/.backup$ file bookmarks.html 
bookmarks.html: HTML document, ASCII text, with very long lines (302)
```

Hmm it seems like a large file. However, it is not a standard UNIX-based file. Let's try to search for the word `password`
in the file using `grep` command.

``` text
leviathan0@gibson:~/.backup$ cat bookmarks.html | grep "password"
<DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is PPIfmI1qsA" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```

Boom we found our first flag. The flag is `PPIfmI1qsA`.



