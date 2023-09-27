# Bandit 16

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

We can establish a connection with the server via ssh, and using the password `JQttfApK4SeyHwDlI9SXGR50qclOAil1`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit16@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit16@bandit.labs.overthewire.org's password: JQttfApK4SeyHwDlI9SXGR50qclOAil1


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

bandit16@bandit:~$ 
```

## Task

--------------

The credentials for the next level can be retrieved by submitting the password of the current level to a port on 
localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. 
Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, 
the others will simply send back to you whatever you send to it.


## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit16@bandit:~$ ls
bandit16@bandit:~$
```

We were given a hint to find a particular port on localhost in between the ports 31000 and 32000. We can use `nmap` for that.

``` text
bandit16@bandit:~$ nmap -sC -sV localhost -p 31000-32000
Starting Nmap 7.80 ( https://nmap.org ) at 2023-09-18 17:34 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00024s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2023-09-18T14:31:48
|_Not valid after:  2023-09-18T14:32:48
31691/tcp open  echo
31790/tcp open  ssl/unknown
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|_    Wrong! Please enter the correct current password
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2023-09-18T14:31:48
|_Not valid after:  2023-09-18T14:32:48
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.80%T=SSL%I=7%D=9/18%Time=65088A23%P=x86_64-pc-linux-g
SF:nu%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20the
SF:\x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Plea
SF:se\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest,
SF:31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\
SF:n")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x
SF:20password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x20
SF:correct\x20current\x20password\n")%r(TerminalServerCookie,31,"Wrong!\x2
SF:0Please\x20enter\x20the\x20correct\x20current\x20password\n")%r(TLSSess
SF:ionReq,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20pa
SF:ssword\n")%r(Kerberos,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x
SF:20current\x20password\n")%r(FourOhFourRequest,31,"Wrong!\x20Please\x20e
SF:nter\x20the\x20correct\x20current\x20password\n")%r(LPDString,31,"Wrong
SF:!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%r(LDA
SF:PSearchReq,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x
SF:20password\n")%r(SIPOptions,31,"Wrong!\x20Please\x20enter\x20the\x20cor
SF:rect\x20current\x20password\n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 98.56 seconds
```

Nmap was able to find five open ports. We must look at the services running to find what's going on. 

- 31046/tcp open  echo
- 31518/tcp open  ssl/echo
- 31691/tcp open  echo
- 31790/tcp open  ssl/unknown
- 31960/tcp open  echo

Alright, we know we are looking for a server that runs SSL. We can ignore ports *31046, 31691, 31960*.

- 31518/tcp open  ssl/echo
- 31790/tcp open  ssl/unknown

For port `31790` we are given this additional information: 

``` text
31790/tcp open  ssl/unknown
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|_    Wrong! Please enter the correct current password
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2023-09-18T14:31:48
|_Not valid after:  2023-09-18T14:32:48
```

So, let's try to connect to this SSL server via the `openssl s_client` command.

``` text
bandit16@bandit:~$ openssl s_client localhost:31790
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN = localhost
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = localhost
verify error:num=10:certificate has expired
notAfter=Sep 18 14:32:48 2023 GMT
verify return:1
depth=0 CN = localhost
notAfter=Sep 18 14:32:48 2023 GMT
verify return:1
---
Certificate chain
 0 s:CN = localhost
   i:CN = localhost
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA1
   v:NotBefore: Sep 18 14:31:48 2023 GMT; NotAfter: Sep 18 14:32:48 2023 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIDCzCCAfOgAwIBAgIEb7jfsDANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjMwOTE4MTQzMTQ4WhcNMjMwOTE4MTQzMjQ4WjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF
/JRVSGUn03lXEGF3AegYY+mPc6ZozCOGy7hYlVO0DfAtuMj3QiiwF7K3NKscjQSU
yPtOA4mfOC+QAmLSC/Kwi7GjAiPKD8u7NgjNES92o2tsfy/UKtDhYxazMFycbesh
eUvZStB7rEpWY3p4AAbVpSGnbiuriTBVNmrKqAz0dDYDFVWsS/K6isQeuahqJ/Nr
LW2WvucEHyvE49dcfc9FiQOqyeV17diRuZhV9cJGh5ld0uEHftocjFLRIWgDuLGn
EMJYPlPxmQi1aDnUU6mvtlUCFAqrMUK+svaaEMuOF6ljrgHb0NVJ7QlVwM6JCLpi
/F6eg2hLENneSkKVkwhfAgMBAAGjZTBjMBQGA1UdEQQNMAuCCWxvY2FsaG9zdDBL
BglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0ZWQgYnkgTmNhdC4g
U2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3DQEBBQUAA4IBAQCW
cS0a0udS+PuAsBPaDO9li2pcRiiWmMFtZMjskpk7ggmu+dWwy1Hyhe15mwhtRMHc
mSIDcmpnsQhXnNdygPkASDYuBMvEF3x2lrtevEc/tsgWI0jiu4GHY9/xgV1yi0g/
/cj9uEeYZu9f742QV0XVfcVIljCSvT3Ic47MerUKttZ19RVpALuGaWk8++a535x9
gOtfFUs1LGXuphHVvB1Kdj5icD74PV1ZFXV6e7TheMUgrMjJb93T3cyM8IWIuEGu
Z/garZZVV4DetxbSioTgZx7FPdnQ+xg7/gMqsSeoV1ouTN5gEkOmva+qkk2+C+FC
jNEIr/7SZciUS0Nimpcm
-----END CERTIFICATE-----
subject=CN = localhost
issuer=CN = localhost
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1339 bytes and written 373 bytes
Verification error: certificate has expired
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 2048 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 10 (certificate has expired)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 6A55900D6148BAC16C75C03884440169BEED5FE9FD31B5CB0FE6A1F5411AAEB6
    Session-ID-ctx: 
    Resumption PSK: D5682C8241A9D6646FAC009696B5E099C302ED28249CD89EA193331F4F4B1FDA25D286EF22546C6712FC11A3014F1727
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - f1 d7 8c 2f 7c 5d 01 37-ac 93 cd d3 c4 31 18 b8   .../|].7.....1..
    0010 - 62 e3 f1 98 e6 c1 97 71-75 33 e8 2c ee 01 14 b1   b......qu3.,....
    0020 - 56 f9 8e 96 ce 47 88 ad-1e 6a e3 5c 39 30 aa bc   V....G...j.\90..
    0030 - 6f dc 87 6a 95 8a 00 1b-ae 08 68 c4 d2 38 33 25   o..j......h..83%
    0040 - 41 57 2c cd 3b 0e 61 ae-c5 ec 30 23 c1 38 1f 5c   AW,.;.a...0#.8.\
    0050 - c6 ca 8b 15 27 49 80 96-f7 e1 23 7f e6 06 a4 4a   ....'I....#....J
    0060 - 1a 51 e1 51 b5 26 bf 46-a2 63 af 9c 97 0b e6 74   .Q.Q.&.F.c.....t
    0070 - 82 ec bc 1f 69 82 73 d3-f0 f9 a4 64 28 da 1a 9a   ....i.s....d(...
    0080 - a0 ea 6a f0 c7 da 1a 08-7b e2 5f 31 ae 84 0e b6   ..j.....{._1....
    0090 - 83 1f 26 ba 2d 9a 7d 12-2e e0 be ca 60 b4 39 fb   ..&.-.}.....`.9.
    00a0 - d9 be 32 93 a0 e0 72 42-a0 51 6e 0d 2a 55 33 20   ..2...rB.Qn.*U3 
    00b0 - d3 f1 1a fe 74 82 83 9d-40 7a e6 a8 3f 55 7c eb   ....t...@z..?U|.
    00c0 - e3 85 4f 73 82 23 21 94-d9 c8 38 0a a1 3a 36 40   ..Os.#!...8..:6@

    Start Time: 1695059191
    Timeout   : 7200 (sec)
    Verify return code: 10 (certificate has expired)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 82FBB0480B19A33795FB8AEBB0C972B18A9DC4BAA413F8C16C4283E17C3262DA
    Session-ID-ctx: 
    Resumption PSK: 2E19B520FC99AA25D726136B2B794A333EE04EDB85B1174E1285A870C2D2C083FB56D832D89E4AB6F64BDEF105028A9B
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - f1 d7 8c 2f 7c 5d 01 37-ac 93 cd d3 c4 31 18 b8   .../|].7.....1..
    0010 - 89 08 ae 0a a7 a1 d4 fe-19 87 53 11 8c 66 43 a9   ..........S..fC.
    0020 - f4 c8 1b b8 e4 9b 5a c5-9e ef 33 cb 9c f7 f5 c9   ......Z...3.....
    0030 - 48 ba 15 10 ac b2 8d 42-d4 11 97 aa 61 5e 9c 61   H......B....a^.a
    0040 - cf 41 fd 5d 26 5c 40 ea-3b ab ac 05 68 c9 8e 67   .A.]&\@.;...h..g
    0050 - 7f fc 73 67 53 ed 90 2c-e8 f8 55 f9 6e 79 c3 fd   ..sgS..,..U.ny..
    0060 - 46 16 0e b5 11 99 77 a6-20 98 84 3a 52 1c c3 83   F.....w. ..:R...
    0070 - 8c 47 2d 63 71 55 ba b1-4f 6c 76 91 b5 5b cd 7b   .G-cqU..Olv..[.{
    0080 - 56 78 dc d5 f5 de 1e 53-d4 7c 13 23 6b ae c8 1e   Vx.....S.|.#k...
    0090 - 82 43 d9 72 74 6b ff 43-93 3d 5f 9d 81 f2 7e f3   .C.rtk.C.=_...~.
    00a0 - fc f4 24 da 3b a1 4e 52-d2 b0 e7 24 ba ea ee ae   ..$.;.NR...$....
    00b0 - a9 aa b3 fd 6c 96 d6 92-dc c2 ae ba 6d 98 aa b2   ....l.......m...
    00c0 - cd 9b 5c 40 30 9f 4e 56-d2 05 2b ba 43 c8 76 c6   ..\@0.NV..+.C.v.

    Start Time: 1695059191
    Timeout   : 7200 (sec)
    Verify return code: 10 (certificate has expired)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK

```

This looks exactly like the last level. Let's submit the password for the current level and see what happens.

``` text
<snip>
---
read R BLOCK
JQttfApK4SeyHwDlI9SXGR50qclOAil1
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

closed
bandit16@bandit:~$ 
```

Hmm. This does not look like a flag. However, it might be the *RSA PRIVATE KEY* for the next level.

Let's create a temporary file and save the key in that file.

``` text
bandit16@bandit:~$ touch shh_login.private
touch: cannot touch 'shh_login.private': Permission denied
bandit16@bandit:~$ cd /tmp
bandit16@bandit:/tmp$ touch shh_login.private
bandit16@bandit:/tmp$
```

Let's add the private key to the file via `echo` command.

``` text
bandit16@bandit:/tmp$ echo -n "-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----" > ssh_login.private
```

Let's submit the PRIVATE RSA KEY to the bandit17@etc host via the `ssh -i` flag.

``` text
bandit16@bandit:~$ ssh bandit17@bandit.labs.overthewire.org -p 2220 -i /tmp/ssh_login.private
The authenticity of host '[bandit.labs.overthewire.org]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit16/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit16/.ssh/known_hosts).
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

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0664 for '/tmp/ssh_login.private' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/tmp/ssh_login.private": bad permissions
bandit17@bandit.labs.overthewire.org: Permission denied (publickey).
```

Oh this is interesting. Our key file is `too open` therefore it's being ignored. 

``` text
Permissions 0664 for '/tmp/ssh_login.private' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
```

So, we can use the `chmod` command to change the file permissions.

``` text
+---------+---------------+------------+
|  Access | Symbolic Mode | Octal Mode |
+---------+---------------+------------+
|  Read   |        r      |      4     |
+---------+---------------+------------+
|  Write  |        w      |      2     |
+---------+---------------+------------+
| Execute |        x      |      1     |
+---------+---------------+------------+
( User, Group, Others )
Ex: 700 -> -rwx------ 
User has read, write and execute rights of the file or directory.
However, Group and Others do not have any rights (No Read, No Write, No Execute.)
```
       
Alright let's change the rights of the file.

``` text
bandit16@bandit:~$ chmod 600 /tmp/ssh_login.private
```

Alright let's try it again.

``` text
bandit16@bandit:~$ ssh bandit17@bandit.labs.overthewire.org -p 2220 -i /tmp/ssh_login.private
The authenticity of host '[bandit.labs.overthewire.org]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit16/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit16/.ssh/known_hosts).
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

bandit17@bandit:~$
```

Alright we are logged in. Let's print the password from `/etc/bandit_pass/bandit17`

``` text
bandit17@bandit:~$ cat /etc/bandit_pass/bandit17
VwOSWtCA7lRKkTfbr2IDh6awj9RNZM5e
```

Boom we got another flag.


