# Krypton 6


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
➜  ~  ssh krypton4@krypton.labs.overthewire.org -p 2231
                      _                     _              
                     | | ___ __ _   _ _ __ | |_ ___  _ __  
                     | |/ / '__| | | | '_ \| __/ _ \| '_ \ 
                     |   <| |  | |_| | |_) | || (_) | | | |
                     |_|\_\_|   \__, | .__/ \__\___/|_| |_|
                                |___/|_|                   

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

krypton4@krypton.labs.overthewire.org's password: 
```

The password will always be the flag of the previous level. With the exception of Level 0, where the password is `KRYPTONISGREAT`.


## Connecting

--------------

We can establish a connection with the server via ssh, and using the password `RANDOM`:

``` text
➜  ~  ssh krypton6@krypton.labs.overthewire.org -p 2231
                      _                     _              
                     | | ___ __ _   _ _ __ | |_ ___  _ __  
                     | |/ / '__| | | | '_ \| __/ _ \| '_ \ 
                     |   <| |  | |_| | |_) | || (_) | | | |
                     |_|\_\_|   \__, | .__/ \__\___/|_| |_|
                                |___/|_|                   

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

krypton6@krypton.labs.overthewire.org's password: RANDOM

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

krypton6@gibson:~$ 
```

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).


``` text
krypton6@bandit:~$ cd /krypton/krypton6
krypton6@bandit:/krypton/krypton6$ ls
encrypt6  HINT1  HINT2  keyfile.dat  krypton7  onetime  README
krypton6@bandit:/krypton/krypton6$ cat README 
Hopefully by now its obvious that encryption using repeating keys
is a bad idea.  Frequency analysis can destroy repeating/fixed key
substitution crypto.

A feature of good crypto is random ciphertext.  A good cipher must
not reveal any clues about the plaintext.  Since natural language 
plaintext (in this case, English) contains patterns, it is left up
to the encryption key or the encryption algorithm to add the 
'randomness'.

Modern ciphers are similar to older plain substitution 
ciphers, but improve the 'random' nature of the key.

An example of an older cipher using a complex, random, large key
is a vigniere using a key of the same size of the plaintext.  For
example, imagine you and your confident have agreed on a key using
the book 'A Tale of Two Cities' as your key, in 256 byte blocks.

The cipher works as such:

Each plaintext message is broken into 256 byte blocks.  For each 
block of plaintext, a corresponding 256 byte block from the book
is used as the key, starting from the first chapter, and progressing.
No part of the book is ever re-used as key.  The use of a key of the 
same length as the plaintext, and only using it once is called a "One Time Pad".

Look in the krypton6/onetime  directory.  You will find a file called 'plain1', a 256 
byte block.  You will also see a file 'key1', the first 256 bytes of
'A Tale of Two Cities'.  The file 'cipher1' is the cipher text of 
plain1.  As you can see (and try) it is very difficult to break
the cipher without the key knowledge.

(NOTE - it is possible though.  Using plain language as a one time pad
key has a weakness.  As a secondary challenge, open README in that directory)

If the encryption is truly random letters, and only used once, then it
is impossible to break.  A truly random "One Time Pad" key cannot be
broken.  Consider intercepting a ciphertext message of 1000 bytes.  One
could brute force for the key, but due to the random key nature, you would
produce every single valid 1000 letter plaintext as well.  Who is to know
which is the real plaintext?!?

Choosing keys that are the same size as the plaintext is impractical.
Therefore, other methods must be used to obscure ciphertext against 
frequency analysis in a simple substitution cipher.  The
impracticality of an 'infinite' key means that the randomness, or
entropy, of the encryption is introduced via the method.

We have seen the method of 'substitution'.  Even in modern crypto,
substitution is a valid technique.  Another technique is 'transposition',
or swapping of bytes.

Modern ciphers break into two types; symmetric and asymmetric.

Symmetric ciphers come in two flavours: block and stream.

Until now, we have been playing with classical ciphers, approximating
'block' ciphers.  A block cipher is done in fixed size blocks (suprise!).
For example, in the previous paragraphs we discussed breaking text and keys
into 256 byte blocks, and working on those blocks.  Block ciphers use a
fixed key to perform substituion and transposition ciphers on each
block discretely.

Its time to employ a stream cipher.  A stream cipher attempts to create
an on-the-fly 'random' keystream to encrypt the incoming plaintext one
byte at a time.  Typically, the 'random' key byte is xor'd with the 
plaintext to produce the ciphertext.  If the random keystream can be
replicated at the recieving end, then a further xor will produce the
plaintext once again.

From this example forward, we will be working with bytes, not ASCII 
text, so a hex editor/dumper like hexdump is a necessity.  Now is the
right time to start to learn to use tools like cryptool.

In this example, the keyfile is in your directory, however it is 
not readable by you.  The binary 'encrypt6' is also available.
It will read the keyfile and encrypt any message you desire, using
the key AND a 'random' number.  You get to perform a 'known ciphertext'
attack by introducing plaintext of your choice.  The challenge here is 
not simple, but the 'random' number generator is weak.

As stated, it is now that we suggest you begin to use public tools, like cryptool,
to help in your analysis.  You will most likely need a hint to get going.
See 'HINT1' if you need a kicktstart.

If you have further difficulty, there is a hint in 'HINT2'.

The password for level 7 (krypton7) is encrypted with 'encrypt6'.

Good Luck!
```

Let's check out the hints.

``` text
krypton6@bandit:/krypton/krypton6$ cat HINT*
The 'random' generator has a limited number of bits, and is periodic.
Entropy analysis and a good look at the bytes in a hex editor will help.

There is a pattern!
8 bit LFSR
krypton6@bandit:/krypton/krypton6$ 
```

Alright. Let's try to encrypt some text to see if we can find a pattern.

``` text
krypton6@bandit:/krypton/krypton6$ mktemp -d
/tmp/tmp.Jqk58XVQu5
krypton6@bandit:/krypton/krypton6$ /tmp/tmp.Jqk58XVQu5
-bash: /tmp/tmp.Jqk58XVQu5: Is a directory
krypton6@bandit:/krypton/krypton6$ cd /tmp/tmp.Jqk58XVQu5
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ ln -s /krypton/krypton6/keyfile.dat 
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ python3 -c 'print("A"*100)' > plain_a.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ touch cipher_a.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ chmod 777 .
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ /krypton/krypton6/encrypt6 plain_a.txt cipher_a.txt 
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ cat cipher_a.txt 
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZ
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ 
```

Okay. We can see that some letters get repeated after a certain amount. Let's use `python3` to figure out exactly how
many characters there are until there is a repetition.

``` text
krypton6@bandit:/krypton/krypton6$ python3
Python 3.10.6 (main, Mar 10 2023, 10:55:28) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> len("EICTDGYIYZKTHNSIRFXYCPFUEOCKRN")
30
>>>
```

Okay. There is 30 letters until we reach `EICT...` again. We can use `fold -w 30` to break the lines into 30 char blocks.

``` text
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ cat cipher_a.txt | fold -w 30
EICTDGYIYZKTHNSIRFXYCPFUEOCKRN
EICTDGYIYZKTHNSIRFXYCPFUEOCKRN
EICTDGYIYZKTHNSIRFXYCPFUEOCKRN
EICTDGYIYZ
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ 
```

As expected, the pattern simply repeats.

Let's try the letters `B` and `C`. 

``` text
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ python3 -c 'print("B"*100)' > plain_b.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ python3 -c 'print("C"*100)' > plain_c.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ touch cipher_b.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ touch cipher_c.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ /krypton/krypton6/encrypt6 plain_b.txt cipher_b.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ /krypton/krypton6/encrypt6 plain_c.txt cipher_c.txt
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ cat cipher_b.txt | fold -w 30
FJDUEHZJZALUIOTJSGYZDQGVFPDLSO
FJDUEHZJZALUIOTJSGYZDQGVFPDLSO
FJDUEHZJZALUIOTJSGYZDQGVFPDLSO
FJDUEHZJZA
krypton6@bandit:/tmp/tmp.Jqk58XVQu5$ cat cipher_c.txt | fold -w 30
GKEVFIAKABMVJPUKTHZAERHWGQEMTP
GKEVFIAKABMVJPUKTHZAERHWGQEMTP
GKEVFIAKABMVJPUKTHZAERHWGQEMTP
GKEVFIAKAB
```

As we can see, between the different ciphers, they increment by one. Meaning, `E->F->G->...` This means, there are specified
shift values in place for each of the 30 chars in the block. This is because, every letter increases by `ONE` if we increase
the plaintext characters by `ONE`. 

So, we now need to find the shift values. We can use `python3` for this. 

Firstly, how do we find the shift value? We can use the `Unicode` enumeration for this.

``` text
krypton6@bandit:/krypton/krypton6$ python3
Python 3.10.6 (main, Mar 10 2023, 10:55:28) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> ord("A")
65
>>> ord("B")
66
>>> ord("C")
67
>>> 
```

We can simply subtract each character from `EICTDGYIYZKTHNSIRFXYCPFUEOCKRN` to find the shift values.

``` text
>>> temp = []
>>> for x in "EICTDGYIYZKTHNSIRFXYCPFUEOCKRN":
...     temp.append(ord(x)-65)
... 
>>> temp
[4, 8, 2, 19, 3, 6, 24, 8, 24, 25, 10, 19, 7, 13, 18, 8, 17, 5, 23, 24, 2, 15, 5, 20, 4, 14, 2, 10, 17, 13]
>>> 
```

The opposite of `ord()` is the `chr()` function. Which turns an integer into the equivalent `Unicode` representation.

This means, we can use `temp` and subtract each character of the `krypton7 -> PNUKLYLWRQKGKBE` with the equivalent 
element of `temp`.

``` text
>>> password = ""
>>> for x,y in zip("PNUKLYLWRQKGKBE", temp):
...     password += chr(ord(x)-y)
... 
>>> password
'LFS8IS4O:8A4D53'
>>> 
```

Hmm. After trying the output as a password for the `krypton7` level, but that didn't work. So we made a mistake somewhere.
A flaw in our thinking.

Let's check out what the element of the array looks like when we have digits in our `password` text.

``` text
L F S 8 I S 4 O : 8 A 4 D 5 3
| | | | | | | | | | | | | | |
     19    24 25 19 ... etc
```

Hmm. So we notice how only the numbers have a corresponding element that is `12+`. So, we probably have a wraparound problem.

Let's check with `krypton7` file.

``` text
>>> [ord(x) for x in "PNUKLYLWRQKGKBE"]
[80, 78, 85, 75, 76, 89, 76, 87, 82, 81, 75, 71, 75, 66, 69]
[ 4,  8,  2, 19,  3,  6, 24,  8, 24, 25... <- shift amount.
```

It might be a wrap_around problem, where we have to shift the other way if we go above a number `12` shift. So, we have
to subtract `26` from all numbers that are large than `12`.

``` text
>>> temp = [4, 8, 2, 19, 3, 6, 24, 8, 24, 25, 10, 19, 7, 13, 18, 8, 17, 5, 23, 24, 2, 15, 5, 20, 4, 14, 2, 10, 17, 13]
>>> temp_wrap = []
>>> for x in temp:
...     if x > 12:
...             x -= 26
...     temp_wrap.append(x)
... 
>>> temp_wrap
[4, 8, 2, -7, 3, 6, -2, 8, -2, -1, 10, -7, 7, -13, -8, 8, -9, 5, -3, -2, 2, -11, 5, -6, 4, -12, 2, 10, -9, -13]
>>> cipher = "PNUKLYLWRQKGKBE"
>>> password = ""
>>> for x,y in zip(cipher, temp_wrap):
...     password += chr(ord(x)-y)
... 
>>> password
'LFSRISNOTRANDOM'
```

And BOOM. We got the password!!


