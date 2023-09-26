# Bandit 24

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

We can establish a connection with the server via ssh, and using the password `YnQpBuifNMas1hcUFk70ZmqkhUU2EuaS`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit27@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit27@bandit.labs.overthewire.org's password: YnQpBuifNMas1hcUFk70ZmqkhUU2EuaS


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

bandit27@bandit:~$ 
```

## Task

--------------

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. 
The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` text
bandit27@bandit:~$ ls
bandit27@bandit:~$
```

We are supposed to `clone` a `git` repository using `ssh`. Let's check the man pages and see if we can find anything 
interesting.

``` text
<snip>
GIT URLS
       In general, URLs contain information about the transport protocol, the address of the remote server, and the path to the repository. Depending on
       the transport protocol, some of this information may be absent.

       Git supports ssh, git, http, and https protocols (in addition, ftp, and ftps can be used for fetching, but this is inefficient and deprecated; do
       not use it).

       The native transport (i.e. git:// URL) does no authentication and should be used with caution on unsecured networks.

       The following syntaxes may be used with them:

       •   ssh://[user@]host.xz[:port]/path/to/repo.git/

       •   git://host.xz[:port]/path/to/repo.git/

       •   http[s]://host.xz[:port]/path/to/repo.git/

       •   ftp[s]://host.xz[:port]/path/to/repo.git/

       An alternative scp-like syntax may also be used with the ssh protocol:

       •   [user@]host.xz:path/to/repo.git/
<snip>
```

Alright after stumbling across the `git-clone` `manpages` we found this section here. Based on this section we can use
the following command to `clone` the repository: `git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo`

Let's test it out by creating a temporary directory and then execute the command in there.

``` text
bandit27@bandit:~$ mktemp -d
/tmp/tmp.ouQxSAVTJu
bandit27@bandit:~$ cd /tmp/tmp.ouQxSAVTJu
bandit27@bandit:/tmp/tmp.ouQxSAVTJu$ git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
Cloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit27/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit27/.ssh/known_hosts).
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit27-git@localhost's password: 
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
bandit27@bandit:/tmp/tmp.ouQxSAVTJu$ ls
repo
```

Boom we got a repo. Let's `cd` in and look around.

``` text
bandit27@bandit:/tmp/tmp.ouQxSAVTJu$ cd repo
bandit27@bandit:/tmp/tmp.ouQxSAVTJu/repo$ ls
README
bandit27@bandit:/tmp/tmp.ouQxSAVTJu/repo$ cat README
The password to the next level is: AVanL161y9rsbcJIsFHuw35rjaOM19nR
```

Hmm. This was pretty easy. But we got the next flag. YEAH!






