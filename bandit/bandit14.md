# Bandit 14

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

We can establish a connection with the server via ssh, and using the password `fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit14@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit14@bandit.labs.overthewire.org's password: fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq


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

## Task

--------------

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.


## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit14@bandit:~$ ls
bandit14@bandit:~$
```

Alright, there are no files in this directory. Judging from the **Task**, we have to connect to the localhost on port 30000
and use the password for this level as the *password* to receive the password for `bandit15`.

Now, how can we do this? We are given some commands we might need from the **Task** thingy.
              

--------------

           
### SSH
ssh (SSH client) is a program for logging into a remote machine and for executing commands on a remote machine.  It is intended to provide secure encrypted communications
between two untrusted hosts over an insecure network.  X11 connections, arbitrary TCP ports and UNIX-domain sockets can also be forwarded over the secure channel.

ssh connects and logs into the specified destination, which may be specified as either [user@]hostname or a URI of the form ssh://[user@]hostname[:port].  The user must
prove their identity to the remote machine using one of several methods (see below).

If a command is specified, it will be executed on the remote host instead of a login shell.  A complete command line may be specified as command, or it may have additional
arguments.  If supplied, the arguments will be appended to the command, separated by spaces, before it is sent to the server to be executed.

### Telnet

user interface to the TELNET protocol
         
The telnet command is used for interactive communication with another host using the TELNET protocol. 
It begins in command mode, where it prints a telnet prompt ("telnet> "). If telnet is invoked with a host argument, 
it performs an open command implicitly; see the description below.


### Netcat

nc – arbitrary TCP and UDP connections and listens

The nc (or netcat) utility is used for just about anything under the sun involving TCP or UDP.  It can open TCP connections, send UDP packets, listen on arbitrary TCP and
UDP ports, do port scanning, and deal with both IPv4 and IPv6.  Unlike telnet(1), nc scripts nicely, and separates error messages onto standard error instead of sending
them to standard output, as telnet(1) does with some.

Netcat or NC is a utility tool that uses TCP and UDP connections to read and write in a network. 
It can be used for both attacking and security. In the case of attacking. It helps us to debug the network along 
with investigating it.

One of the simple wonders of netcat is its ability to transfer files between computers. 
By creating this simple connection, we can then use that connection to transfer files between two computers. 
This can be extremely useful as a network administrator and even more useful as a hacker.

Common uses include:
- simple TCP proxies
- shell-script based HTTP clients and servers
- network daemon testing
- a SOCKS or HTTP ProxyCommand for ssh(1)
- and much, much more


### OpenSSL

OpenSSL is a cryptography toolkit implementing the Secure Sockets Layer (SSL v2/v3) and Transport Layer Security (TLS v1) 
network protocols and related cryptography standards required by them.

The openssl program is a command line program for using the various cryptography functions of OpenSSL's crypto library 
from the shell. It can be used for
  - Creation and management of private keys, public keys and parameters
  - Public key cryptographic operations
  - Creation of X.509 certificates, CSRs and CRLs
  - Calculation of Message Digests and Message Authentication Codes
  - Encryption and Decryption with Ciphers
  - SSL/TLS Client and Server Tests
  - Handling of S/MIME signed or encrypted mail
  - Timestamp requests, generation and verification
### S_Client

The s_client command implements a generic SSL/TLS client which connects to a remote host using SSL/TLS. 
It is a very useful diagnostic tool for SSL servers.

### Nmap

Nmap (“Network Mapper”) is an open source tool for network exploration and security auditing. 
It was designed to rapidly scan large networks, although it works fine against single hosts. 
Nmap uses raw IP packets in novel ways to determine what hosts are available on the network, 
what services (application name and version) those hosts are offering, what operating systems (and OS versions)
they are running, what type of packet filters/firewalls are in use, and dozens of other characteristics. 
While Nmap is commonly used for security audits, many systems and network administrators find it useful for routine 
tasks such as network inventory, managing service upgrade schedules, and monitoring host or service uptime.
              

--------------


Alright. For this exercise we will use Netcat. Why? Using Netcat we can create a so called **Netcat Shell**, which allows us
to connect to the localhost on port 30000. 



``` text
bandit14@bandit:~$ nc localhost 30000
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
Correct!
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
```
 
*Note: After sending `fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq`, the server running on port 30000 processes it in some way, and
returns the password/flag for the next level.

**This is NOT a reverse shell**

Boom we got the flag.


