# Bandit 15

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

We can establish a connection with the server via ssh, and using the password `jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit15@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit15@bandit.labs.overthewire.org's password: jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt


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

bandit15@bandit:~$ 
```

## Task

--------------

The password for the next level can be retrieved by submitting the password of the current level 
to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. 
Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit15@bandit:~$ ls
bandit15@bandit:~$
```


After googling `how can i connect to local host using ssl encryption terminal` we find that the first two websites use
the command `openssl s_client` to achieve a connection using ssl encryption. 

We should dive a little deeper into what SSL encryption actually is, so we can visualize our problem better.


### What is SSL (Secure Sockets Layer)

SSL is a security protocol used to establish encrypted links between a web server and a client in online communication. 
The purpose of SSL is to ensure that all data transferred between the web server and the client remains confidential and secure. 
In the context of web development and Internet security, SSL is essential for protecting sensitive information like login credentials, 
credit card numbers, or any data that needs to be secured.


*Side Note: SSL is technology your applications or browsers may have used to create a secure, 
encrypted communication channel over any network. However, SSL is an older technology that 
contains some security flaws (depending on which version, perhaps major flaws). 
Transport Layer Security (TLS) is the upgraded version of SSL that fixes existing SSL vulnerabilities.*


``` text
bandit15@bandit:~$ openssl s_client -help
Usage: s_client [options] [host:port]

General options:
 -help                      Display this summary
 -engine val                Use engine, possibly a hardware device
 -ssl_client_engine val     Specify engine to be used for client certificate operations
 -ssl_config val            Use specified section for SSL_CTX configuration
 -ct                        Request and parse SCTs (also enables OCSP stapling)
 -noct                      Do not request or parse SCTs (default)
 -ctlogfile infile          CT log list CONF file

Network options:
 -host val                  Use -connect instead

<snip>

 -xcert infile              cert for Extended certificates
 -xchain infile             chain for Extended certificates
 -xchain_build              build certificate chain for the extended certificates
 -xcertform PEM|DER         format of Extended certificate (PEM/DER/P12); has no effect
 -xkeyform PEM|DER          format of Extended certificate's key (DER/PEM/P12); has no effect

Provider options:
 -provider-path val         Provider load path (must be before 'provider' argument if required)
 -provider val              Provider to load (can be specified multiple times)
 -propquery val             Property query used when fetching algorithms

Parameters:
 host:port                  Where to connect; same as -connect option
```

Okay this is a lot of text. However, we can narrow down the "lines of interest" via the `grep` command.


``` text
bandit15@bandit:~$ openssl s_client -help 2>&1 | grep "connect"
 -host val                  Use -connect instead
 -port +int                 Use -connect instead
 -connect val               TCP/IP where to connect; default: 4433)
 -bind val                  bind local address for connection
 -read_buf +int             Default read buffer size to be used for connections
 -reconnect                 Drop and re-make the connection with the same Session-ID
 -brief                     Restrict output to brief summary of connection parameters
 -timeout                   Enable send/receive timeout on DTLS connections
 -legacy_server_connect     Allow initial connection to servers that don't support RI
 -no_legacy_server_connect  Disallow initial connection to servers that don't support RI
 -verify_return_error       Close connection on verification error
 host:port                  Where to connect; same as -connect option
```

Alright, we narrowed down the lines that contained the word `connect`. 
One thing that caught my eye was the last line of the output: `host:port`

Let's try to run `openssl s_client` command now with `localhost:30001` as the parameter.

``` text
bandit15@bandit:~$ openssl s_client localhost:30001
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN = localhost
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = localhost
verify error:num=10:certificate has expired
notAfter=Sep 18 14:32:45 2023 GMT
verify return:1
depth=0 CN = localhost
notAfter=Sep 18 14:32:45 2023 GMT
verify return:1
---
Certificate chain
 0 s:CN = localhost
   i:CN = localhost
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA1
   v:NotBefore: Sep 18 14:31:45 2023 GMT; NotAfter: Sep 18 14:32:45 2023 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIDCzCCAfOgAwIBAgIEX0ZMHDANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjMwOTE4MTQzMTQ1WhcNMjMwOTE4MTQzMjQ1WjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCv
uZRRFzw3NdTa2RqdMyBbsAWUGopIM/cRWtQPIAnuGhd3NFysGrz30A/xP0p1Z2No
bChnVPjwxAcx9WRgxHR8cMyZjJKar0IhpoTvqf5gd0y6wpKHpzwMsKmzfAftkg0P
gzAo3KZiEYYvg4Ro+Yxa5vBVSTl0LaQ/t4vcoH/Sw7JO4tIJ2yt142JpDvpgNS6v
WdIJYvIrxQlbmsrW9QkO72755Oa+cDxdimwtxGet2X5tFs5s4FhF6XdnwwBfnT7c
Gpm379qBu8RpoioW7q/hpQb55kOCk9vADAVwUj9lBFtpoii20xTNj/HPyMRg44vS
DS48k/PIt0V6EQrMphOjAgMBAAGjZTBjMBQGA1UdEQQNMAuCCWxvY2FsaG9zdDBL
BglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0ZWQgYnkgTmNhdC4g
U2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3DQEBBQUAA4IBAQCK
WvdQ9gC/lFFZz72Uv7Kb5ypsjNgMH1CEPwXAa9LQ1ZemaQdLM7WIZDsOT/bW//yP
EjcqkL4csHqgysXjbRQqUgk+U87EEGHSwLjVurN+SlONony9HOTYVkIxiWV2eWy0
C9gpNSUAQFkvJM0g91sGgDwN8fDdwUyyhr5ttKQLVTnE3EEV/j4FEsijEx2ls0W0
Gavgcxw/9rqMWcsFnCMncZVJj3mOWgWWnRdL2WjthOyBZaC0xQZvFmP/Um8B7MSR
Jt0ZEaf3jQyldwtWLj7E3NT8EjYl52/7WDG3QMDV7feCTRTt8IFfV58rGv801Ypo
tjCpdg6uvG3ck02soIoD
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
    Session-ID: 02BAE63180A6EE4EB63F903C7F7231BD30913D00DB80B00495B8B35B1F0ECE92
    Session-ID-ctx: 
    Resumption PSK: AFD24AF8F74DCAB9276B3F32DD03BC9941915504728061C6CE84858C322D149A71EB80FFFE3E0097D9C6B21BFFA6BFBC
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - aa 90 d2 aa 8c 5d 88 78-47 2c 29 eb f6 80 ef d8   .....].xG,).....
    0010 - de 23 8e d6 56 ae 56 88-52 97 23 ea d3 33 d3 9c   .#..V.V.R.#..3..
    0020 - 9b ee 27 47 60 32 e7 44-95 2e 32 68 6f 2a c5 1a   ..'G`2.D..2ho*..
    0030 - 32 3a de c4 cc 85 e4 03-fe bb 33 66 ae 88 eb e5   2:........3f....
    0040 - 48 2d 65 7b 2d d9 cc 49-84 fb 17 7f e8 de 15 ce   H-e{-..I........
    0050 - ca 33 0b 1c 24 be 03 ea-4a fc 0a 3d a3 3c 70 d0   .3..$...J..=.<p.
    0060 - 04 07 a2 d2 9b 73 11 6d-f6 de 39 50 1b 9f f9 12   .....s.m..9P....
    0070 - 93 ed f1 b3 05 22 77 76-f8 9d d5 73 4e b3 d6 06   ....."wv...sN...
    0080 - 92 66 62 07 e1 f7 de e8-79 6f 15 37 69 5d 59 c5   .fb.....yo.7i]Y.
    0090 - 4e da 2c eb a6 65 5c 64-83 0b 34 39 04 50 fd 85   N.,..e\d..49.P..
    00a0 - 6d 47 1f 5f a2 6c 8c 09-29 8b 0e 30 d3 fc 82 54   mG._.l..)..0...T
    00b0 - 88 e6 ff 38 10 1a f3 bc-ff a0 0a 87 81 a9 85 6c   ...8...........l
    00c0 - 87 b3 71 36 fe 88 0b 67-87 80 36 40 c9 72 fb ee   ..q6...g..6@.r..

    Start Time: 1695055828
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
    Session-ID: C7EF9088C0CB5E39A534C25273C2756C3A2B920C852015916F84FF11E97A8D4B
    Session-ID-ctx: 
    Resumption PSK: 807FCF7BEA0E4FDB24518A4EA85A1B5CB378B4086CD39215E55E21A25B5C396DD14D766BC26E47C0FE69A297D3DC93F9
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - aa 90 d2 aa 8c 5d 88 78-47 2c 29 eb f6 80 ef d8   .....].xG,).....
    0010 - 4d e2 e9 ce 33 ea 9a df-ce 1d 9b e9 96 a4 a1 9f   M...3...........
    0020 - a6 36 91 01 a7 70 a5 1e-dc 9a 6b e3 78 d6 c0 6d   .6...p....k.x..m
    0030 - 77 06 3d 83 60 5e be 05-8e 4d c9 03 27 44 19 02   w.=.`^...M..'D..
    0040 - 17 f6 39 21 52 42 a3 f5-2a f2 52 d4 29 08 55 69   ..9!RB..*.R.).Ui
    0050 - a3 82 d2 58 68 8f 15 d2-e6 ac d0 db 0a af 04 5c   ...Xh..........\
    0060 - f6 5f 21 50 46 73 d8 5c-00 34 66 f6 d7 f8 e2 59   ._!PFs.\.4f....Y
    0070 - 13 08 be f4 72 ac ac 98-8c c8 ce d8 3c 3d dd 8e   ....r.......<=..
    0080 - 9c 11 31 19 b0 d8 c1 7a-cb ff 27 30 e9 d9 8d 01   ..1....z..'0....
    0090 - 80 8a f9 f0 3f c2 64 6a-01 c0 4d a2 71 81 65 90   ....?.dj..M.q.e.
    00a0 - 5f 82 20 4d 93 ca 45 b9-cb 53 b4 1a de c2 84 c5   _. M..E..S......
    00b0 - 4a 66 c2 75 ed fb f8 92-cf 8f 86 28 9b 29 e5 5f   Jf.u.......(.)._
    00c0 - 46 e3 85 09 f1 4a c5 de-08 08 ee a7 ed 04 ef 69   F....J.........i

    Start Time: 1695055829
    Timeout   : 7200 (sec)
    Verify return code: 10 (certificate has expired)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK

```

Interesting. We are now in some type of shell. Let's submit the password for `bandit15` (similar to how we got the password for `bandit14`),
and perhaps get a response.

``` text
<snip>
---
read R BLOCK
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
Correct!
JQttfApK4SeyHwDlI9SXGR50qclOAil1

closed
bandit15@bandit:~$ 
```

Holy shit. We got another flag.

**Note: After re-reading the task, I realized we were told to submit the password there. I should really start reading the instructions
more often*


