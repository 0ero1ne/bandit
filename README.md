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

In this one the we have to `cd` into `inhere` directory and an hidden file 
named `.hidden` or simply `cat` from the HOME folder typing the path.

```
bandit3@bandit:~$ cat inhere/.hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

## Level 4 → Level 5

We are looking for a human-readble file, most likely a ASCII file.  
Using `find` combined with `file` will output the type of each file we found, 
then we can `grep ASCII` to parse only the file-type we are looking for and 
pipe it to `cat` to see its content.  
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
Use `find` again filtering `-size 33c`, `-user bandit7` and `-group bandit6`.  
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

First we need to find a way to work on `data.txt` and because we have no permission in our `HOME` folder we have to make a new one inside `/tmp/`, `cd` into it and `cp` the file inside.

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

If just want to skip the tedious process here's the whole list of commands in order.

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




## Level 14 → Level 15
## Level 15 → Level 16
## Level 16 → Level 17
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
