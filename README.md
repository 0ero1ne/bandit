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

There is a file named `-`, `cat` it using `./`.

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
Use `find` again filtering the `-size 33c`, `-user bandit7` and `-group bandit6`.  
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

The file `data.txt` is binary, to read its content we can use `strings` to print the strings of printable
characters then we can `grep` the one that has multiple `==` in it.

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



## Level 12 → Level 13
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
