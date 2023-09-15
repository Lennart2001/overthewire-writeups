# Bandit 12

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

We can establish a connection with the server via ssh, and using the password `JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv`:

``` commandline
┌──(francis㉿ghost)-[~]
└─$ ssh bandit12@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit12@bandit.labs.overthewire.org's password: JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv


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

bandit12@bandit:~$ 
```

## Task

--------------

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. 
For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. 
Then copy the datafile using cp, and rename it using mv (read the manpages!)

## Looking Around

--------------
We first start by looking around the home directory, as we have no idea what is going on. We will use the command `ls`,
which allows us to `list` the content of the current directory (or specified directory).

``` commandline
bandit12@bandit:~$ ls
data.txt
```
Okay we got another data.txt file. Let's print the first few lines with `head`.

``` commandline
bandit12@bandit:~$ head data.txt 
00000000: 1f8b 0808 2773 4564 0203 6461 7461 322e  ....'sEd..data2.
00000010: 6269 6e00 0145 02ba fd42 5a68 3931 4159  bin..E...BZh91AY
00000020: 2653 597b 4f96 5f00 0018 ffff fd6f e7ed  &SY{O._......o..
00000030: bff7 bef7 9fdb d7ca ffbf edff 8ded dfd7  ................
00000040: bfe7 bbff bfdb fbff ffbf ff9f b001 3b56  ..............;V
00000050: 0400 0068 0064 3400 d341 a000 0680 0699  ...h.d4..A......
00000060: 0000 69a0 0000 1a00 1a0d 0034 0034 d3d4  ..i........4.4..
00000070: d1a3 d464 6834 6403 d469 b422 0d00 3400  ...dh4d..i."..4.
00000080: 1a68 068d 3403 4d06 8d00 0c80 00f5 0003  .h..4.M.........
00000090: 4031 3119 00d0 1a68 1a34 c86d 4640 00d0  @11....h.4.mF@..
```

Hmmm. This challenge might be... challenging. Lol

#### Hexdump

In computing, a hex dump is a hexadecimal view (on screen or paper) of computer data, from memory or from a computer file or storage device. 
Looking at a hex dump of data is usually done in the context of either debugging, reverse engineering or digital forensics.

In a hex dump, each byte (8 bits) is represented as a two-digit hexadecimal number. 
Hex dumps are commonly organized into rows of 8 or 16 bytes, sometimes separated by whitespaces. 
Some hex dumps have the hexadecimal memory address at the beginning.

Some common names for this program function are `hexdump`, `hd`, `od`, `xxd` and simply `dump` or even `D`.

----------

Since we have a hexdump with unknown stuff on the right side. We can use the `xxd` command to **reverse** the hexdump.

``` text
DESCRIPTION
       xxd  creates  a hex dump of a given file or standard input.  It can also convert a hex dump back to its original binary form.  Like uuencode(1) and
       uudecode(1) it allows the transmission of binary data in a `mail-safe' ASCII representation, but has the advantage of decoding to standard  output.
       Moreover, it can be used to perform binary file patching.

<snip>

OPTIONS
<snip>
    -r | -revert
          Reverse operation: convert (or patch) hexdump into binary.  If not writing to stdout, xxd writes into its output file without truncating it.
          Use the combination -r -p to read plain hexadecimal dumps without line number information and without a particular column layout. Additional
          Whitespace and line-breaks are allowed anywhere.
<snip>
```

We first need to create a file where we can put the output. 

``` commandline
bandit12@bandit:~$ touch data_out.txt
touch: cannot touch 'data_out.txt': Permission denied
```

Oh. We don't have permission to create files in that directory. In the task, it was hinted that we should create a directory
in the /tmp directory and copy the file there. So, let's do exactly that.


``` commandline
bandit12@bandit:~$ mkdir /tmp/var
bandit12@bandit:~$ cp data.txt /tmp/var
bandit12@bandit:/tmp/var$ ls
data.txt
```

We copied the data.txt file to */tmp/var*. In here we can create new files with `touch`.

``` commandline
bandit12@bandit:/tmp/var$ touch data_out.txt
bandit12@bandit:/tmp/var$ ls
data_out.txt  data.txt
```

We now can write the output of `xxd -revert data.txt` to `data_out.txt`.

``` commandline
bandit12@bandit:/tmp/var$ xxd -revert data.txt > data_out.txt 
```

We can now check out what's going on in the file via the `file` command.

``` commandline
bandit12@bandit:/tmp/var$ file data_out.txt 
data_out.txt: gzip compressed data, was "data2.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 581
```

We can now check out the `man pages` for gzip.

``` text
NAME
       gzip, gunzip, zcat - compress or expand files

SYNOPSIS
       gzip [ -acdfhklLnNrtvV19 ] [-S suffix] [ name ...  ]
       gunzip [ -acfhklLnNrtvV ] [-S suffix] [ name ...  ]
       zcat [ -fhLV ] [ name ...  ]

DESCRIPTION
       Gzip  reduces  the size of the named files using Lempel-Ziv coding (LZ77).  Whenever possible, each file is replaced by one with the extension .gz,
       while keeping the same ownership modes, access and modification times.  (The default extension is z for MSDOS, OS/2 FAT, Windows NT FAT and Atari.)
       If  no  files are specified, or if a file name is "-", the standard input is compressed to the standard output.  Gzip will only attempt to compress
       regular files.  In particular, it will ignore symbolic links.

       If the compressed file name is too long for its file system, gzip truncates it.  Gzip attempts to truncate only the parts of the file  name  longer
       than  3 characters.  (A part is delimited by dots.) If the name consists of small parts only, the longest parts are truncated. For example, if file
       names are limited to 14 characters, gzip.msdos.exe is compressed to gzi.msd.exe.gz.  Names are not truncated on systems which do not have  a  limit
       on file name length.

       By  default, gzip keeps the original file name and timestamp in the compressed file. These are used when decompressing the file with the -N option.
       This is useful when the compressed file name was truncated or when the timestamp was not preserved after a file transfer.

       Compressed files can be restored to their original form using gzip -d or gunzip or zcat.  If the original name saved in the compressed file is  not
       suitable for its file system, a new name is constructed from the original one to make it legal.

       gunzip  takes a list of files on its command line and replaces each file whose name ends with .gz, -gz, .z, -z, or _z (ignoring case) and which be‐
       gins with the correct magic number with an uncompressed file without the original extension.  gunzip also recognizes the  special  extensions  .tgz
       and  .taz  as  shorthands for .tar.gz and .tar.Z respectively.  When compressing, gzip uses the .tgz extension if necessary instead of truncating a
       file with a .tar extension.

       gunzip can currently decompress files created by gzip, zip, compress, compress -H or pack.  The detection of the input format is  automatic.   When
       using  the  first two formats, gunzip checks a 32 bit CRC. For pack and gunzip checks the uncompressed length. The standard compress format was not
       designed to allow consistency checks. However gunzip is sometimes able to detect a bad .Z file. If you get an error when uncompressing a  .Z  file,
       do  not  assume that the .Z file is correct simply because the standard uncompress does not complain. This generally means that the standard uncom‐
       press does not check its input, and happily generates garbage output.  The SCO compress -H format (lzh compression method) does not include  a  CRC
       but also allows some consistency checks.

       Files created by zip can be uncompressed by gzip only if they have a single member compressed with the 'deflation' method. This feature is only in‐
       tended to help conversion of tar.zip files to the tar.gz format.  To extract a zip file with a single member, use a command like gunzip <foo.zip or
       gunzip -S .zip foo.zip.  To extract zip files with several members, use unzip instead of gunzip.

       zcat  is  identical  to gunzip -c.  (On some systems, zcat may be installed as gzcat to preserve the original link to compress.)  zcat uncompresses
       either a list of files on the command line or its standard input and writes the uncompressed data on standard output.  zcat will  uncompress  files
       that have the correct magic number whether they have a .gz suffix or not.

       Gzip uses the Lempel-Ziv algorithm used in zip and PKZIP.  The amount of compression obtained depends on the size of the input and the distribution
       of common substrings.  Typically, text such as source code or English is reduced by  60-70%.   Compression  is  generally  much  better  than  that
       achieved by LZW (as used in compress), Huffman coding (as used in pack), or adaptive Huffman coding (compact).

       Compression  is always performed, even if the compressed file is slightly larger than the original. The worst case expansion is a few bytes for the
       gzip file header, plus 5 bytes every 32K block, or an expansion ratio of 0.015% for large files. Note that the actual number of  used  disk  blocks
       almost never increases.  gzip preserves the mode, ownership and timestamps of files when compressing or decompressing.
```

The most important paragraph for our purposes is the following:

``` text
Compressed files can be restored to their original form using gzip -d or gunzip or zcat.  
If the original name saved in the compressed file is  not suitable for its file system, 
a new name is constructed from the original one to make it legal.
```

We now try to decompress the file using `gzip`.

``` commandline
bandit12@bandit:/tmp/var$ gzip -d data_out.txt
gzip: data_out.txt: unknown suffix -- ignored
```

Hmm apparently gzip specifically looks for a `.gz` file extension. Let's change the file's extension using `mv` and try
again.

``` commandline
bandit12@bandit:/tmp/var$ mv data_out.txt data_out.txt.gz
bandit12@bandit:/tmp/var$ ls
data_out.txt.gz  data.txt
bandit12@bandit:/tmp/var$ gzip -d data_out.txt.gz 
bandit12@bandit:/tmp/var$ ls
data_out.txt  data.txt
```

Let's use `file` command to see if we even changed the file.

``` commandline
bandit12@bandit:/tmp/var$ file data_out.txt 
data_out.txt: bzip2 compressed data, block size = 900k
```

Yes. We definitely decompressed the file. Hmm, but now we have a new compression named `bzip2`. Let's do a quick look up using
the `man pages`.

``` text
NAME
       bzip2, bunzip2 - a block-sorting file compressor, v1.0.8
       bzcat - decompresses files to stdout
       bzip2recover - recovers data from damaged bzip2 files

SYNOPSIS
       bzip2 [ -cdfkqstvzVL123456789 ] [ filenames ...  ]
       bzip2 [ -h|--help ]
       bunzip2 [ -fkvsVL ] [ filenames ...  ]
       bunzip2 [ -h|--help ]
       bzcat [ -s ] [ filenames ...  ]
       bzcat [ -h|--help ]
       bzip2recover filename

DESCRIPTION
       bzip2  compresses files using the Burrows-Wheeler block sorting text compression algorithm, and Huffman coding.  Compression is generally consider‐
       ably better than that achieved by more conventional LZ77/LZ78-based compressors, and approaches the performance of the PPM  family  of  statistical
       compressors.

       The command-line options are deliberately very similar to those of GNU gzip, but they are not identical.

       bzip2  expects  a  list  of file names to accompany the command-line flags.  Each file is replaced by a compressed version of itself, with the name
       "original_name.bz2".  Each compressed file has the same modification date, permissions, and, when possible, ownership as the  corresponding  origi‐
       nal, so that these properties can be correctly restored at decompression time.  File name handling is naive in the sense that there is no mechanism
       for preserving original file names, permissions, ownerships or dates in filesystems which lack these concepts, or have serious file name length re‐
       strictions, such as MS-DOS.

       bzip2 and bunzip2 will by default not overwrite existing files.  If you want this to happen, specify the -f flag.

       If no file names are specified, bzip2 compresses from standard input to standard output.  In this case, bzip2 will decline to write compressed out‐
       put to a terminal, as this would be entirely incomprehensible and therefore pointless.

       bunzip2 (or bzip2 -d) decompresses all specified files.  Files which were not created by bzip2 will be detected and ignored, and a warning  issued.
       bzip2 attempts to guess the filename for the decompressed file from that of the compressed file as follows:

              filename.bz2    becomes   filename
              filename.bz     becomes   filename
              filename.tbz2   becomes   filename.tar
              filename.tbz    becomes   filename.tar
              anyothername    becomes   anyothername.out

       If  the file does not end in one of the recognised endings, .bz2, .bz, .tbz2 or .tbz, bzip2 complains that it cannot guess the name of the original
       file, and uses the original name with .out appended.

<snip>

OPTIONS
       -c --stdout
              Compress or decompress to standard output.

       -d --decompress
              Force decompression.  bzip2, bunzip2 and bzcat are really the same program, and the decision about what actions to take is done on the basis
              of which name is used.  This flag overrides that mechanism, and forces bzip2 to decompress.

<snip>
```

Now we can use the `bzip2 --decompress` command + flag to decompress the data_out.txt file. We expect the file to turn into
`data_out.txt.out` as described in the `man pages`.

``` commandline
bandit12@bandit:/tmp/var$ bzip2 -d data_out.txt
bzip2: Can't guess original name for data_out.txt -- using data_out.txt.out
bandit12@bandit:/tmp/var$ ls
data_out.txt.out  data.txt
```

Alright. We decompressed the file using `bzip2` let's check out what the file is now using `file` command.

``` commandline
bandit12@bandit:/tmp/var$ file data_out.txt.out 
data_out.txt.out: gzip compressed data, was "data4.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 20480
```

Oh. Another `gzip` compression. Let's change the extension and decompress using `gzip --decompress`.

``` commandline
bandit12@bandit:/tmp/var$ mv data_out.txt.out data_out.txt.gz
bandit12@bandit:/tmp/var$ ls
data_out.txt.gz  data.txt
bandit12@bandit:/tmp/var$ gzip --decompress data_out.txt.gz 
bandit12@bandit:/tmp/var$ ls
data_out.txt  data.txt
```

Alright we successfully decompressed the file using `gzip`. Let's check out what the file is now using `file` command.

``` commandline
bandit12@bandit:/tmp/var$ file data_out.txt 
data_out.txt: POSIX tar archive (GNU)
```

Hmm. It appears the now have a tar file. Let's check out the tar `man pages`.

``` text
NOTE
       This manpage is a short description of GNU tar.  For a detailed discussion, including examples and usage recommendations, refer to the GNU Tar Man‐
       ual available in texinfo format.  If the info reader and the tar documentation are properly installed on your system, the command

           info tar

       should give you access to the complete manual.

       You can also view the manual using the info mode in emacs(1), or find it in various formats online at

           http://www.gnu.org/software/tar/manual

       If any discrepancies occur between this manpage and the GNU Tar Manual, the later shall be considered the authoritative source.
```

For our purposes, these pages are enough info.

``` text
Operation mode
<snip>
        -x, --extract, --get
                      Extract files from an archive.  Arguments are optional.  When given, they specify names of the archive members to be extracted.
<snip>
```

Alright. We found an appropriate flag to use with our file. Let's try it.

``` commandline
bandit12@bandit:/tmp/var$ tar -x data_out.txt
tar: Refusing to read archive contents from terminal (missing -f option?)
tar: Error is not recoverable: exiting now
bandit12@bandit:/tmp/var$ tar -xf data_out.txt
bandit12@bandit:/tmp/var$ ls
data5.bin  data_out.txt  data.txt
bandit12@bandit:/tmp/var$ file data5.bin 
data5.bin: POSIX tar archive (GNU)
```

Alright. We got the tar file, which revealed another tar file. Let's extract the contents again.

``` commandline
bandit12@bandit:/tmp/var$ tar -xf data5.bin
bandit12@bandit:/tmp/var$ ls
data5.bin  data6.bin  data_out.txt  data.txt
bandit12@bandit:/tmp/var$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
```

Alright after extracting the contents of `data5.bin` we got file `data6.bin` and after checking what's going on with the file
using the `file` command. We see that it's another bzip2 compressed file. Let's decompress that file using the `bzip2' command.

``` commandline
bandit12@bandit:/tmp/var$ bzip2 --decompress data6.bin
bzip2: Can't guess original name for data6.bin -- using data6.bin.out
bandit12@bandit:/tmp/var$ ls
data5.bin  data6.bin.out  data_out.txt  data.txt
bandit12@bandit:/tmp/var$ file data6.bin.out 
data6.bin.out: POSIX tar archive (GNU)
```

Alright. It's another tar file. Let's extract using `tar -xf`.

``` commandline
bandit12@bandit:/tmp/var$ tar -xf data6.bin.out
bandit12@bandit:/tmp/var$ ls
data5.bin  data6.bin.out  data8.bin  data_out.txt  data.txt
bandit12@bandit:/tmp/var$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 49
```

Okay. After extracting the file, the new file is `data8.bin`. After examining the file using `file` command, we find that 
the new file is gzip compressed. Let's decompress the file using `gzip --decompress`.

``` commandline
bandit12@bandit:/tmp/var$ gzip --decompress data8.bin
gzip: data8.bin: unknown suffix -- ignored
bandit12@bandit:/tmp/var$ mv data8.bin data8.bin.gz
bandit12@bandit:/tmp/var$ gzip --decompress data8.bin.gz
bandit12@bandit:/tmp/var$ ls
data5.bin  data6.bin.out  data8.bin  data_out.txt  data.txt
```

Keep in mind that we need to change the file's extension to `.gz` so `gzip` is able to to decompress the file.

Alright. Let's check what's going on with the file using the `file` command.

``` commandline
bandit12@bandit:/tmp/var$ file data8.bin
data8.bin: ASCII text
```

Oh man could it be? We found the flag?? Let's `cat` the file and check it out.

``` commandline
bandit12@bandit:/tmp/var$ cat data8.bin
The password is wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

BOOM. We got the flag.


