# Krypton 2


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
➜  ~  ssh krypton2@krypton.labs.overthewire.org -p 2231
                      _                     _              
                     | | ___ __ _   _ _ __ | |_ ___  _ __  
                     | |/ / '__| | | | '_ \| __/ _ \| '_ \ 
                     |   <| |  | |_| | |_) | || (_) | | | |
                     |_|\_\_|   \__, | .__/ \__\___/|_| |_|
                                |___/|_|                   

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

krypton2@krypton.labs.overthewire.org's password: 
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `KRYPTONISGREAT`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `KRYPTONISGREAT`:

``` text
➜  ~  ssh krypton2@krypton.labs.overthewire.org -p 2231
                      _                     _              
                     | | ___ __ _   _ _ __ | |_ ___  _ __  
                     | |/ / '__| | | | '_ \| __/ _ \| '_ \ 
                     |   <| |  | |_| | |_) | || (_) | | | |
                     |_|\_\_|   \__, | .__/ \__\___/|_| |_|
                                |___/|_|                   

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

krypton2@krypton.labs.overthewire.org's password: KRYPTONISGREAT

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

krypton2@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
krypton2@bandit:~$ cd /krypton/krypton2
krypton2@bandit:/krypton/krypton2$ cat README 
Krypton 2

ROT13 is a simple substitution cipher.

Substitution ciphers are a simple replacement algorithm.  In this example
of a substitution cipher, we will explore a 'monoalphebetic' cipher.
Monoalphebetic means, literally, "one alphabet" and you will see why.

This level contains an old form of cipher called a 'Caesar Cipher'.
A Caesar cipher shifts the alphabet by a set number.  For example:

plain:	a b c d e f g h i j k ...
cipher:	G H I J K L M N O P Q ...

In this example, the letter 'a' in plaintext is replaced by a 'G' in the
ciphertext so, for example, the plaintext 'bad' becomes 'HGJ' in ciphertext.

The password for level 3 is in the file krypton3.  It is in 5 letter
group ciphertext.  It is encrypted with a Caesar Cipher.  Without any 
further information, this cipher text may be difficult to break.  You do 
not have direct access to the key, however you do have access to a program 
that will encrypt anything you wish to give it using the key.  
If you think logically, this is completely easy.

One shot can solve it!

Have fun.

Additional Information:

The `encrypt` binary will look for the keyfile in your current working
directory. Therefore, it might be best to create a working direcory in /tmp
and in there a link to the keyfile. As the `encrypt` binary runs setuid
`krypton3`, you also need to give `krypton3` access to your working directory.

Here is an example:

krypton2@melinda:~$ mktemp -d
/tmp/tmp.Wf2OnCpCDQ
krypton2@melinda:~$ cd /tmp/tmp.Wf2OnCpCDQ
krypton2@melinda:/tmp/tmp.Wf2OnCpCDQ$ ln -s /krypton/krypton2/keyfile.dat
krypton2@melinda:/tmp/tmp.Wf2OnCpCDQ$ ls
keyfile.dat
krypton2@melinda:/tmp/tmp.Wf2OnCpCDQ$ chmod 777 .
krypton2@melinda:/tmp/tmp.Wf2OnCpCDQ$ /krypton/krypton2/encrypt /etc/issue
krypton2@melinda:/tmp/tmp.Wf2OnCpCDQ$ ls
ciphertext  keyfile.dat

krypton2@bandit:/krypton/krypton2$
```


``` text
krypton2@bandit:/krypton/krypton2$ ls
encrypt  keyfile.dat  krypton3  README
krypton2@bandit:/krypton/krypton2$ cat krypton3 
OMQEMDUEQMEK
```

We first check out the files in the "home" directory. There is the encrypted `krypton3` file in there with `OMQEMDUEQMEK`
as its contents. We then create a temporary directory, so we can create custom files. 

``` text
krypton2@bandit:/krypton/krypton2$ mktemp -d
/tmp/tmp.qPe1iDjSgK
krypton2@bandit:/krypton/krypton2$ cd /tmp/tmp.qPe1iDjSgK
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ ln -s /krypton/krypton2/keyfile.dat 
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ echo "aaaaaaaaaaaaaaaaaaa" > plain.txt
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ chmod 777 .
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ /krypton/krypton2/encrypt plain.txt
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ ls
ciphertext  keyfile.dat  plain.txt
krypton2@bandit:/tmp/tmp.qPe1iDjSgK$ cat ciphertext 
MMMMMMMMMMMMMMMMMMMkrypton2@bandit:/tmp/tmp.qPe1iDjSgK$ 
```

Following the *instructions* from the `README` file, we must create a symbolic link to the `keyfile.dat` file, as the
file allows us to encrypt more files and the `encrypt` file checks out the `current working directory` to try to find the
`keyfile.dat` file. 

We also create a `plain.txt` file, filled with `aaaaaaaa...` to see how many steps the `ceasar cipher` takes.

We must also make the directory accessible to everyone, so `chmod 777 .` gets called. 

Now, we have everything in place to encrypt the `plain.txt` file. And the output was `MMMMMMMM...` which lets us know
that we do not have a changing ceaser cipher. Now, we can further deduce that we have rotated the alphabet by 12 steps.

But how did we find this out? Well, we did it the easy way first just writting out the *new* alphabet: `MNOPQRS...ABCDEF...`.
But in a mathematical sense, it would be:

``` text
0)  a-a
1)  a-b
2)  a-c
3)  a-d
4)  a-e
5)  a-f
6)  a-g
7)  a-h
8)  a-i
9)  a-j
10) a-k
11) a-l
12) a-m
...
```

Alright. The rotation was 12. 

We can use the following commands to decrypt the `krypton3` file: `cat krypton3 | tr "M-ZA-L" "A-Z"`.

``` text
krypton2@bandit:/krypton/krypton2$ cat krypton3 | tr "M-ZA-L" "A-Z"
CAESARISEASY
```

BOOM we got the password for the next level. 




