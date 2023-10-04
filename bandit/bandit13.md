# Bandit 13

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

We can establish a connection with the server via ssh, and using the password `wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit13@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit13@bandit.labs.overthewire.org's password: wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw


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

bandit13@bandit:~$ 
```

## Task

--------------

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. 
For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. 
Note: localhost is a hostname that refers to the machine you are working on

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit12@bandit:~$ ls
sshkey.private
```

Oh this is a new file we haven't seen before. Let's try to `cat` the file and see what's in it.

``` text
bandit13@bandit:~$ cat sshkey.private 
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----
```

Alright. The file contains a RSA private key. Let's do some googling to search for RSA Private keys and ssh. 

Let's also check out the `man pages` for the `ssh` command.

``` text
NAME
     ssh — OpenSSH remote login client

<snip>

DESCRIPTION
     ssh (SSH client) is a program for logging into a remote machine and for executing commands on a remote machine.  It is intended to provide secure en‐
     crypted communications between two untrusted hosts over an insecure network.  X11 connections, arbitrary TCP ports and UNIX-domain sockets can also
     be forwarded over the secure channel.

     ssh connects and logs into the specified destination, which may be specified as either [user@]hostname or a URI of the form
     ssh://[user@]hostname[:port].  The user must prove their identity to the remote machine using one of several methods (see below).

     If a command is specified, it will be executed on the remote host instead of a login shell.  A complete command line may be specified as command, or
     it may have additional arguments.  If supplied, the arguments will be appended to the command, separated by spaces, before it is sent to the server
     to be executed.

<snip>

    -i identity_file
                 Selects a file from which the identity (private key) for public key authentication is read.  You can also specify a public key file to use
                 the corresponding private key that is loaded in ssh-agent(1) when the private key file is not present locally.  The default is ~/.ssh/id_rsa,
                 ~/.ssh/id_ecdsa, ~/.ssh/id_ecdsa_sk, ~/.ssh/id_ed25519, ~/.ssh/id_ed25519_sk and ~/.ssh/id_dsa.  Identity files may also be specified on a
                 per-host basis in the configuration file.  It is possible to have multiple -i options (and multiple identities specified in configuration
                 files).  If no certificates have been explicitly specified by the CertificateFile directive, ssh will also try to load certificate informa‐
                 tion from the filename obtained by appending -cert.pub to identity filenames.
<snip>
```

Okay. We found a flag which allows us to pass in a *private_key* file. 

Let's try it. 

``` text
bandit13@bandit:~$ ssh bandit14@bandit.labs.overthewire.org -p 2220 -i sshkey.private 
The authenticity of host '[bandit.labs.overthewire.org]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit13/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit13/.ssh/known_hosts).
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

!!! You are trying to log into this SSH server with a password on port 2220 from localhost.
!!! Connecting from localhost is blocked to conserve resources.
!!! Please log out and log in again.


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

bandit14@bandit:~$ 
```

No way! We were able to enter `bandit14` via the *ssh_private_key*.

Now, we need to go to directoyr where all the passwords are saved for the individual levels.

``` text
bandit14@bandit:~$ cd /etc/bandit_pass
```

Let's show the contents of the directory.

``` text
bandit14@bandit:/etc/bandit_pass$ ls -lah
total 152K
drwxr-xr-x   2 root     root     4.0K Apr 23 18:04 .
drwxr-xr-x 108 root     root      12K Aug 12 08:42 ..
-r--------   1 bandit0  bandit0     8 Apr 23 18:04 bandit0
-r--------   1 bandit1  bandit1    33 Apr 23 18:04 bandit1
-r--------   1 bandit10 bandit10   33 Apr 23 18:04 bandit10
-r--------   1 bandit11 bandit11   33 Apr 23 18:04 bandit11
-r--------   1 bandit12 bandit12   33 Apr 23 18:04 bandit12
-r--------   1 bandit13 bandit13   33 Apr 23 18:04 bandit13
-r--------   1 bandit14 bandit14   33 Apr 23 18:04 bandit14
-r--------   1 bandit15 bandit15   33 Apr 23 18:04 bandit15
-r--------   1 bandit16 bandit16   33 Apr 23 18:04 bandit16
-r--------   1 bandit17 bandit17   33 Apr 23 18:04 bandit17
-r--------   1 bandit18 bandit18   33 Apr 23 18:04 bandit18
-r--------   1 bandit19 bandit19   33 Apr 23 18:04 bandit19
-r--------   1 bandit2  bandit2    33 Apr 23 18:04 bandit2
-r--------   1 bandit20 bandit20   33 Apr 23 18:04 bandit20
-r--------   1 bandit21 bandit21   33 Apr 23 18:04 bandit21
-r--------   1 bandit22 bandit22   33 Apr 23 18:04 bandit22
-r--------   1 bandit23 bandit23   33 Apr 23 18:04 bandit23
-r--------   1 bandit24 bandit24   33 Apr 23 18:04 bandit24
-r--------   1 bandit25 bandit25   33 Apr 23 18:04 bandit25
-r--------   1 bandit26 bandit26   33 Apr 23 18:04 bandit26
-r--------   1 bandit27 bandit27   33 Apr 23 18:04 bandit27
-r--------   1 bandit28 bandit28   33 Apr 23 18:04 bandit28
-r--------   1 bandit29 bandit29   33 Apr 23 18:04 bandit29
-r--------   1 bandit3  bandit3    33 Apr 23 18:04 bandit3
-r--------   1 bandit30 bandit30   33 Apr 23 18:04 bandit30
-r--------   1 bandit31 bandit31   33 Apr 23 18:04 bandit31
-r--------   1 bandit32 bandit32   33 Apr 23 18:04 bandit32
-r--------   1 bandit33 bandit33   33 Apr 23 18:04 bandit33
-r--------   1 bandit4  bandit4    33 Apr 23 18:04 bandit4
-r--------   1 bandit5  bandit5    33 Apr 23 18:04 bandit5
-r--------   1 bandit6  bandit6    33 Apr 23 18:04 bandit6
-r--------   1 bandit7  bandit7    33 Apr 23 18:04 bandit7
-r--------   1 bandit8  bandit8    33 Apr 23 18:04 bandit8
-r--------   1 bandit9  bandit9    33 Apr 23 18:04 bandit9
```

As we can see, only the user `bandit14` has access to **read** the contents of file `bandit14`.

Let's print the contents of the `bandit14` file, since we are currently logged in as user `bandit14`.

``` text
bandit14@bandit:/etc/bandit_pass$ cat bandit14
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
```

Boom we got another flag.

