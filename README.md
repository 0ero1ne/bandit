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
```

Paste the password.

```
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

closed
bandit15@bandit:~$
```

## Level 16 → Level 17

Scan the `localhost` with `nmap` on port range `31000-32000`.  
There are 5 open ports where we can connect to.  
Try all 5 to see which one speaks `ssl` and will repond with a `RSA` key.  

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
```

Connect using `openssl` on port `31790`.

```
bandit16@bandit:~$ openssl s_client -connect localhost:31790
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
MIICBjCCAW+gAwIBAgIEYXGqnDANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjAwOTAzMjAxNzI2WhcNMjEwOTAzMjAxNzI2WjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBANVNNACn
qTf4L4RLdjuJiNMJwI28fiHq0NvJF31V7K8cSjKnePgSlykp6tht9/8eNoOIQX/r
Zx6r6sUBQpfvzBCWU3PkGVcVW5cC5Av6vmBGOvJGv030NwD22wF9BYpIDHaCQUPq
3DULeAJxRHSUvHjE+/g4DneII9S3ouyh4XLlAgMBAAGjZTBjMBQGA1UdEQQNMAuC
CWxvY2FsaG9zdDBLBglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0
ZWQgYnkgTmNhdC4gU2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3
DQEBBQUAA4GBAKbV691eONbzAVUORkbEqc974FMohpW41PLasyaxtcr2M9kR3GE9
FLQmQ3DScKAR0RxJy3KyPcycneC3RMOCudgPZGK7XF3MJQKDNOdOu4Bckp96CQC5
6nrC3qIegoiRUO0Uj9WUo83AjlWJ//Se7WW0R5SVwwG9ld490NeQFcgL
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
    Session-ID: 925A5C7A4FA56D009E0A4D4DEAF92CFF94C350F643A08685E2B5BA52B391F7BE
    Session-ID-ctx:
    Master-Key: 35DABB2C6E3784E39436796B281D5EABC658781C01969F8EAEFDB3726DFFAD9383DEE8918E6041669215252CA79A5DF0
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - a6 a6 3d 62 3a 43 47 bd-a0 d7 e4 c9 79 c6 f6 3b   ..=b:CG.....y..;
    0010 - df 20 bb e0 af d6 9b 7c-8e fb dd 00 f8 32 24 8c   . .....|.....2$.
    0020 - c9 79 b3 71 44 47 17 68-3a cb 5a 78 38 05 7b 0e   .y.qDG.h:.Zx8.{.
    0030 - 48 1a 11 8a 47 e9 80 a3-c9 20 46 42 97 d9 1a a1   H...G.... FB....
    0040 - d6 f5 18 fb d7 fe 4e 17-48 a2 06 7e 4b ce a6 db   ......N.H..~K...
    0050 - 46 49 0d 63 6e c4 2b 03-03 c0 0a ad d9 cf 11 7c   FI.cn.+........|
    0060 - 01 b5 98 87 d5 b2 cb d5-64 14 4b c0 6a 68 73 8a   ........d.K.jhs.
    0070 - 1d 2a 8b fb b5 de 61 aa-73 ac df 05 77 3e 70 56   .*....a.s...w>pV
    0080 - 12 06 4d 11 df d4 f9 71-42 82 b6 7d dd ca 01 7e   ..M....qB..}...~
    0090 - 9e c7 19 6c a3 c0 9b f2-32 70 b3 ac ff b3 bc de   ...l....2p......

    Start Time: 1602412695
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: yes
---
```

Now paste the password.

```
cluFn7wTiGryunymYOu4RcffSxQluehd
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

## Level 17 → Level 18

Copy that key and use it to connect to `bandit17`.

```
+ elliot ~/Coding/overthewire → ssh -i key17 bandit17@bandit.labs.overthewire.org -p 2220
```

Now you can cat the password for this level and won't need the sshkey anymore.




There are two files in the `HOME` directory: `passwords.new` and `passwords.old`.  
Use the command `diff` to find the only different line, the second one it's the right one.

```
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
---
> w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
bandit17@bandit:~$
```

## Level 18 → Level 19

To connect to `bandit19` you'll have to disable the pseudo terminal allocation using `ssh` with the flag `-T`.

```
+ elliot ~/Coding/overthewire → ssh bandit18@bandit.labs.overthewire.org -p 2220 -T
```

After the login message you'll have an empty prompt, just `ls` and cat the `readme`.

```
ls
readme
cat readme
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

## Level 19 → Level 20

Sticky bit or SUID is our the next argument.  
Inside the `HOME` folder there is a binary with `SUDO` permission.

```
bandit19@bandit:~$ ls -al
total 28
drwxr-xr-x  2 root     root     4096 May  7 20:14 .
drwxr-xr-x 41 root     root     4096 May  7 20:14 ..
-rwsr-x---  1 bandit20 bandit19 7296 May  7 20:14 bandit20-do
-rw-r--r--  1 root     root      220 May 15  2017 .bash_logout
-rw-r--r--  1 root     root     3526 May 15  2017 .bashrc
-rw-r--r--  1 root     root      675 May 15  2017 .profile
```

As you can see the `s` instead of `x` in the user column.  
We can execute a command with `SUDO` permission via that binary and get the next password.

```
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
bandit19@bandit:~$
```

## Level 20 → Level 21

We are going to use `netcat` to listen to a specific port and use `suconnect` to connect to.  
There are different way to solve this, using `tmux` to split the pane in two or connect with
two instances of `SSH`.  
I'll demonstrate using `tmux`.

```
bandit20@bandit:~$ tmux
```

When `TMUX` is opened press `CTRL` + `B` + `%` the spli the pane in two.  
To move between panes use `CTRL` + `B` + `o`.  

On the left pane use `netcat` to listen on port `32666`.

`bandit20@bandit:~$ nc -l -p 32666`

On the right pane connect with the binary `suconnect` passing the port `32666`.

`bandit20@bandit:~$ ./suconnect 32666`

Go back on the left pane and paste bandit20's password `GbKksEFF4yrVs6il55v6gwY5aVje5f0j`.  
It should look like this.

```
bandit20@bandit:~$ nc -l -p 32666                       │bandit20@bandit:~$ ./suconnect 32666
GbKksEFF4yrVs6il55v6gwY5aVje5f0j                        │Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr                        │Password matches, sending next password
bandit20@bandit:~$                                      │bandit20@bandit:~$
```

Use `CTRL` + `B` + `x` to and type `y` to kill each pane, then `exit`.

## Level 21 → Level 22

Let's find out what is running with `cronjob` and how to use it.

```
bandit21@bandit:~$ ls -al /etc/cron.d/
total 36
drwxr-xr-x  2 root root 4096 Jul 11 15:56 .
drwxr-xr-x 87 root root 4096 May 14 09:41 ..
-rw-r--r--  1 root root   62 May 14 13:40 cronjob_bandit15_root
-rw-r--r--  1 root root   62 Jul 11 15:56 cronjob_bandit17_root
-rw-r--r--  1 root root  120 May  7 20:14 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7 20:14 cronjob_bandit23
-rw-r--r--  1 root root  120 May 14 09:41 cronjob_bandit24
-rw-r--r--  1 root root   62 May 14 14:04 cronjob_bandit25_root
-rw-r--r--  1 root root  102 Oct  7  2017 .placeholder
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Looks like the script writes on a file in the `tmp` folder, just `cat` it
and get the password for the next level.

```
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
bandit21@bandit:~$
```

## Level 22 → Level 23

Same concept as the level before.

```
bandit22@bandit:~$ ls -al /etc/cron.d/
total 36
drwxr-xr-x  2 root root 4096 Jul 11 15:56 .
drwxr-xr-x 87 root root 4096 May 14 09:41 ..
-rw-r--r--  1 root root   62 May 14 13:40 cronjob_bandit15_root
-rw-r--r--  1 root root   62 Jul 11 15:56 cronjob_bandit17_root
-rw-r--r--  1 root root  120 May  7 20:14 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7 20:14 cronjob_bandit23
-rw-r--r--  1 root root  120 May 14 09:41 cronjob_bandit24
-rw-r--r--  1 root root   62 May 14 14:04 cronjob_bandit25_root
-rw-r--r--  1 root root  102 Oct  7  2017 .placeholder
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:~$
```

All we have to do now is copy the commands chain of `mytarget` and use the output to
find the right folder inside `tmp`, then cat the whole `PATH` to show the content.

```
bandit22@bandit:~$ cat /tmp/$(echo I am user bandit23 | md5sum | cut -d ' ' -f 1)
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
bandit22@bandit:~$
```

## Level 23 → Level 24

In this level we are going to write a small script ot get the next password.  
First of all let's take a look at the cronjob.

```
bandit23@bandit:~$ ls -al /etc/cron.d
total 36
drwxr-xr-x  2 root root 4096 Jul 11 15:56 .
drwxr-xr-x 87 root root 4096 May 14 09:41 ..
-rw-r--r--  1 root root   62 May 14 13:40 cronjob_bandit15_root
-rw-r--r--  1 root root   62 Jul 11 15:56 cronjob_bandit17_root
-rw-r--r--  1 root root  120 May  7 20:14 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7 20:14 cronjob_bandit23
-rw-r--r--  1 root root  120 May 14 09:41 cronjob_bandit24
-rw-r--r--  1 root root   62 May 14 14:04 cronjob_bandit25_root
-rw-r--r--  1 root root  102 Oct  7  2017 .placeholder
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done

bandit23@bandit:~$
```

The dir `/var/spool/bandit24/` has `+w` `+x`, we need to copy out script inside that
folder and make the cronjob execute that for us with `bandit24` privilegies.  
But first, we need to make a new directory to write our script.

```
bandit23@bandit:~$ mkdir /tmp/bandit23
bandit23@bandit:~$ cd /tmp/bandit23
bandit23@bandit:/tmp/bandit23$ touch reveal.sh
bandit23@bandit:/tmp/bandit23$ chmod +x reveal.sh
bandit23@bandit:/tmp/bandit23$ vim reveal.sh
```

Simple script that reads the password from `bandit24` and and echo inside a dir into `/tmp/bandit24`.

```
#!/bin/bash

mkdir /tmp/bandit24

cat /etc/bandit_pass/bandit24 > /tmp/bandit24/pass
```

Copy `reveal.sh` into `/var/spool/bandit25/`, then wait for the password to pop inside `/tmp/bandit24/pass`.

```
bandit23@bandit:/tmp/bandit23$ cat /tmp/bandit24/pass
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
bandit23@bandit:/tmp/bandit23$
```

## Level 24 → Level 25

We have to brute force a `xxxx` numbers pin to a local daemon listening on `30002`.  
Let's make a folder inside `tmp` so we can create files.

```
bandit24@bandit:/tmp/bandit24$ mkdir /tmp/bandit24
bandit24@bandit:/tmp/bandit24$ cd /tmp/bandit24
bandit24@bandit:/tmp/bandit24$ 
```

Now we need to create the file with all the combinations.

`for x in $(echo {0000..9999}); do printf "UoMYTrfrBFHyQXmg6gzctqAwO^C1IohZ $x\n" >> b0; done`

Feed `netcat` with that file to start the sequence.

```
bandit24@bandit:/tmp/bandit24$ nc localhost 30002 < b0
...
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

Exiting.
bandit24@bandit:/tmp/bandit24$
```

## Level 25 → Level 26

After connecting to `bandit25` we are going to use the `sshkey` for `bandit26`.  
Before it we need to force `more` to split the banner into different lines.  
To do so just resize your terminal to 1 line and launch `ssh`.

`bandit25@bandit:~$ ssh -i bandit26.sshkey bandit26@localhost`

As you can see now you are inside more's visual mode.  
Press `v` to open vi/vim. 
`ESCAPE` + `:` and write `set shell=/bin/bash`.  
`ESCAPE` + `:` and write `shell` and press return.

Now that you have a shell you can `cat` the password for `bandit26`.

```
bandit26@bandit:~$ cat /etc/bandit_pass/bandit26
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
bandit26@bandit:~$
```

Finally you can use the binary `bandit27-do` to cat the password for `bandit27`.

```
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
3ba3118a22e93127a4ed485be72ef5ea
bandit26@bandit:~$
```

## Level 26 → Level 27

Test

```

```

## Level 27 → Level 28
## Level 28 → Level 29
## Level 29 → Level 30
## Level 30 → Level 31
## Level 31 → Level 32
## Level 32 → Level 33
