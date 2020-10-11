# Bandit - OverTheWire.com

This is a guide to complete the Bandit machine.

[Bandit Description](https://overthewire.org/wargames/bandit/)

To connect to the box you need `ssh` installed.

## Level 0

The goal is pretty simple: connect to the machine as `bandit0`
using the password `bandit0`.  

``ssh bandit.labs.overthewire.org -u bandit0 -p 2220``

## Level 0 → Level 1

We are inside the home directory of bandit0, to find the flag
just `cat` the file `readme`.  

```
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

## Level 1 → Level 2

There is a file named `-` in the home folder, `cat` it using `./`.

```
bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

## Level 2 → Level 3

The password for the next level is stored inside `spaces in this filename`.  
To `cat` a file that has spaces in its name you can either enter the first characters
of the nama, press `tab` and let BASH complete the name for you, or just type `\` before any
spaces.

```
bandit2@bandit:~$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

## Level 3 → Level 4

In this one the we have to `cd` into `inhere` and `cat` an hidden file named `.hidden`.  
You can simply `cat` it from the `HOME` folder.

```
bandit3@bandit:~$ cat inhere/.hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

## Level 4 → Level 5

We are looking for a human-readble file, most likely an ASCII file.  
Using `find` combined with `file` allow us to see each file's type.  
Then we can `grep ASCII` to parse only the file-type we are looking for and pipe it to `cat` to output its content.  
This is a oneline solution but can be done in different steps.

```
bandit4@bandit:~$ find inhere/ -type f -exec file {} \; | grep "ASCII" | cat $(cut -d':' -f1)
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

## Level 5 → Level 6

Follwing the same concept as before we can use `find` but we are going to filter permissions using `-perm -u-x` and size `-size 1033c`.  
At the end just execute the `cat` command to show file content.  
If we want to trim the output to have a cleaner format we can use `tr -d ' '`.

```
bandit5@bandit:~$ find inhere/ -type f -perm -u-x -size 1033c -exec cat {} \; | tr -d ' '
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

## Level 6 → Level 7

This time we are going to do a more extended search since we don't know the exact path of the file.  
Use `find` with few flags: `-size 33c`, `-user bandit7` and `-group bandit6`.  
Executing the command without `sudo` permissions can output some error messages, we want to redirect those using `2>/dev/null`, then just use `cat` as usual to output the content.

```
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c -exec cat {} \; 2>/dev/null
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

## Level 7 → Level 8

Using `grep` we search for the `millionth` string inside `data.txt`.

```
bandit7@bandit:~$ grep "millionth" data.txt
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

## Level 8 → Level 9

To find the unique string inside `data.txt` just `sort` the file and pipe it into `uniq -u` to print only
unique lines.

```
bandit8@bandit:~$ sort data.txt | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

## Level 9 → Level 10

The file `data.txt` contains just few human-readble strings, to read those we can use `strings` to print the strings of printable characters then we can `grep` the one that has multiple `==` in it.

```
bandit9@bandit:~$ strings data.txt | grep "=="
========== the*2i"4
========== password
Z)========== is
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

## Level 10 → Level 11

Decode the base64 data inside the file `data.txt` using `base64`.

```
bandit10@bandit:~$ base64 -d data.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

## Level 11 → Level 12

Simple ROT13 decode.  
To solve this you can either use [Decode ROT13](http://www.decode.org/) or the `tr` command.  
For the sake of it we are going to use the CLI.

```
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

## Level 12 → Level 13

First we need to find a way to work on `data.txt` and because we have no permissionis in our `HOME` folder we have to copy it into a new one, which is `/tmp/`, then, `cd` into it and `cp` the file inside.

```
bandit12@bandit:~$ mkdir /tmp/bandit12
bandit12@bandit:~$ cd /tmp/bandit12
bandit12@bandit:/tmp/bandit12$ cp ~/data.txt .
bandit12@bandit:/tmp/bandit12$
```

Now let's reverse the hexdump into the actual file.
**NOTE:** if you just execute `xxd` without redirecting you will see `data2.bin` in the output.

```
bandit12@bandit:/tmp/bandit12$ xxd -r data.txt > data2.bin
bandit12@bandit:/tmp/bandit12$
```

Let's find out what type of file is `data2.bin`.

```
bandit12@bandit:/tmp/bandit12$ file data2.bin
data2.bin: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/bandit12$
```

Let's rename `data2.bin` into `data2.bin.gz` and extract it using `gzip`.

```
bandit12@bandit:/tmp/bandit12$ mv data2.bin{,.gz}; gzip -d data2.bin.gz
bandit12@bandit:/tmp/bandit12$ ls -al
total 844
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:49 .
drwxrws-wt 3731 root     root 847872 Oct 11 10:49 ..
-rw-r--r--    1 bandit12 root    573 Oct 11 10:43 data2.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
bandit12@bandit:/tmp/bandit12$
```

The process will be the same until we will have an `ASCII` file.

```
bandit12@bandit:/tmp/bandit12$ file data2.bin
data2.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/bandit12$ mv data2.bin{,.bz}; bzip2 -d data2.bin.bz
bandit12@bandit:/tmp/bandit12$ ls -al
total 844
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:54 .
drwxrws-wt 3733 root     root 847872 Oct 11 10:54 ..
-rw-r--r--    1 bandit12 root    431 Oct 11 10:53 data2.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ mv data{2.bin,4.bin.gz}; gzip -d data4.bin.gz
bandit12@bandit:/tmp/bandit12$ ls -al
total 860
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:57 .
drwxrws-wt 3733 root     root 847872 Oct 11 10:57 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data4.bin
data4.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/bandit12$ mv data4.bin{,.tar}; tar -xf data4.bin.tar
bandit12@bandit:/tmp/bandit12$ ls -al
total 872
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:58 .
drwxrws-wt 3733 root     root 847872 Oct 11 10:58 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data5.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/bandit12$ mv data5.bin{,.tar}; tar -xf data5.bin.tar
bandit12@bandit:/tmp/bandit12$ ls -al
total 876
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:58 .
drwxrws-wt 3733 root     root 847872 Oct 11 10:58 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data5.bin.tar
-rw-r--r--    1 bandit12 root    222 May  7 20:14 data6.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/bandit12$ mv data6.bin{,.bz}; bzip2 -d data6.bin.bz
bandit12@bandit:/tmp/bandit12$ ls -al
total 884
drwxr-sr-x    2 bandit12 root   4096 Oct 11 10:59 .
drwxrws-wt 3734 root     root 847872 Oct 11 11:00 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data5.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data6.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data6.bin
data6.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/bandit12$ mv data6.bin{,.tar}; tar -xf data6.bin.tar
bandit12@bandit:/tmp/bandit12$ ls -al
total 888
drwxr-sr-x    2 bandit12 root   4096 Oct 11 11:00 .
drwxrws-wt 3735 root     root 847872 Oct 11 11:00 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data5.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data6.bin.tar
-rw-r--r--    1 bandit12 root     79 May  7 20:14 data8.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/bandit12$ mv data8.bin{,.gz}; gzip -d data8.bin.gz
bandit12@bandit:/tmp/bandit12$ ls -al
total 888
drwxr-sr-x    2 bandit12 root   4096 Oct 11 11:01 .
drwxrws-wt 3735 root     root 847872 Oct 11 11:01 ..
-rw-r--r--    1 bandit12 root  20480 Oct 11 10:53 data4.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data5.bin.tar
-rw-r--r--    1 bandit12 root  10240 May  7 20:14 data6.bin.tar
-rw-r--r--    1 bandit12 root     49 May  7 20:14 data8.bin
-rw-r-----    1 bandit12 root   2582 Oct 11 10:42 data.txt
```

```
bandit12@bandit:/tmp/bandit12$ file data8.bin
data8.bin: ASCII text
bandit12@bandit:/tmp/bandit12$ cat data8.bin
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
bandit12@bandit:/tmp/bandit12$
```

If you just want to skip the tedious process here's the whole list of commands in order.

```
mkdir /tmp/bandit12
cd /tmp/bandit12
cp ~/data.txt .
xxd -r data.txt > data2.bin
mv data2.bin{,.gz}; gzip -d data2.bin.gz
mv data2.bin{,.bz}; bzip2 -d data2.bin.bz
mv data{2.bin,4.bin.gz}; gzip -d data4.bin.gz
mv data4.bin{,.tar}; tar -xf data4.bin.tar
mv data5.bin{,.tar}; tar -xf data5.bin.tar
mv data6.bin{,.bz}; bzip2 -d data6.bin.bz
mv data6.bin{,.tar}; tar -xf data6.bin.tar
mv data8.bin{,.gz}; gzip -d data8.bin.gz
cat data8.bin
```

## Level 13 → Level 14

In the `HOME` folder there is a sshkey file that can be use to login into `bandit14`.
Aftet that `cat` the password from `/etc/bandit_pass/bandit14`.

```
bandit13@bandit:~$ ssh -i sshkey.private bandit14@localhost
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
bandit14@bandit:~$
```

## Level 14 → Level 15

Simply `nc` on the `localhost` on port `30000` and paste the password of `bandit14`.

```
bandit14@bandit:~$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr

bandit14@bandit:~$
```

## Level 15 → Level 16

Use `openssl` to connect to `localhost` on port `30001`.
Paste the current level password `BfMYroe26WYalil77FoDi9qh59eK5xNr` to get the next password.


```
bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
---
Certificate chain
 0 s:/CN=localhost
   i:/CN=localhost
---
Server certificate
-----BEGIN CERTIFICATE-----
MIICBjCCAW+gAwIBAgIEDU18oTANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjAwNTA3MTgxNTQzWhcNMjEwNTA3MTgxNTQzWjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAK3CPNFR
FEypcqUa8NslmIMWl9xq53Cwhs/fvYHAvauyfE3uDVyyX79Z34Tkot6YflAoufnS
+puh2Kgq7aDaF+xhE+FPcz1JE0C2bflGfEtx4l3qy79SRpLiZ7eio8NPasvduG5e
pkuHefwI4c7GS6Y7OTz/6IpxqXBzv3c+x93TAgMBAAGjZTBjMBQGA1UdEQQNMAuC
CWxvY2FsaG9zdDBLBglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0
ZWQgYnkgTmNhdC4gU2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3
DQEBBQUAA4GBAC9uy1rF2U/OSBXbQJYuPuzT5mYwcjEEV0XwyiX1MFZbKUlyFZUw
rq+P1HfFp+BSODtk6tHM9bTz+p2OJRXuELG0ly8+Nf/hO/mYS1i5Ekzv4PL9hO8q
PfmDXTHs23Tc7ctLqPRj4/4qxw6RF4SM+uxkAuHgT/NDW1LphxkJlKGn
-----END CERTIFICATE-----
subject=/CN=localhost
issuer=/CN=localhost
---
No client certificate CA names sent
Peer signing digest: SHA512
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1019 bytes and written 269 bytes
Verification error: self signed certificate
---
New, TLSv1.2, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 1024 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: E46B506F1287D1627B998D88B2F0A59CC73358550D7CB89C6C8FB1E28B8F7DE9
    Session-ID-ctx:
    Master-Key: 361B5CAB8B612A39D7A736C1A6F4286AA298FA4663EBF988037C30D736B5E9DA48C80F6EB5A70DE3DD8B066F89DFB1D8
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - aa 02 e6 3a 2e 0b c8 5d-6f 54 4a 1b 5a e0 2c 0e   ...:...]oTJ.Z.,.
    0010 - fb 82 db 09 48 99 33 46-8f 92 f0 0c bb bc 6f 20   ....H.3F......o
    0020 - 3d a2 1f 9b 50 f3 81 28-79 6f cd 59 32 b4 18 cf   =...P..(yo.Y2...
    0030 - 36 19 c6 05 7a e1 2d 55-b0 e0 46 70 3f 90 af af   6...z.-U..Fp?...
    0040 - f5 3b b4 ae 0e 6a ee e7-26 ab 08 50 36 80 e1 73   .;...j..&..P6..s
    0050 - ee 40 04 73 05 c3 5d 96-c2 42 9b bf 5f ef a8 23   .@.s..]..B.._..#
    0060 - d9 e5 55 6c 80 cb c8 98-d6 30 02 f2 34 db 83 f1   ..Ul.....0..4...
    0070 - 34 d0 bc cc ab bd 98 34-d7 e7 37 5f 5e c3 0a 8a   4......4..7_^...
    0080 - 75 84 0f 7e 76 40 c0 f2-fe 00 c9 bb 15 57 8c 4d   u..~v@.......W.M
    0090 - fa 42 14 7e 9e d9 c7 92-b9 0b 20 d4 5e fd a7 84   .B.~...... .^...

    Start Time: 1602412129
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: yes
---
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

closed
bandit15@bandit:~$
```

## Level 16 → Level 17

```
bandit16@bandit:~$ nmap localhost -p31000-32000

Starting Nmap 7.40 ( https://nmap.org ) at 2020-10-11 12:32 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00024s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.09 seconds
bandit16@bandit:~$
```



## Level 17 → Level 18
## Level 18 → Level 19
## Level 19 → Level 20
## Level 20 → Level 21
## Level 21 → Level 22
## Level 22 → Level 23
## Level 23 → Level 24
## Level 24 → Level 25
## Level 25 → Level 26
## Level 26 → Level 27
## Level 27 → Level 28
## Level 28 → Level 29
## Level 29 → Level 30
## Level 30 → Level 31
## Level 31 → Level 32
## Level 32 → Level 33
