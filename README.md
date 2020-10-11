# Bandit - OverTheWire.com

This is a guide to complete the Bandit machine.

[Bandit Machine](https://overthewire.org/wargames/bandit/)

To connect to the box you need **ssh** installed.

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
of the nama, press `tab` and let BASH complete the name for you, or just type `\` at
the end of each words.

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

We are looking for a human-readble file, most likely a *ASCII* file.  
Using `find` combined with `file` will output the type of each file we found,  
then we can `grep ASCII` to parse only the file-type we are looking for and  
pipe it to `cat` to see its content.
This is a oneline solution but can be done in different steps.

```
bandit4@bandit:~$ find inhere/ -type f -exec file {} \; | grep "ASCII" | cat $(cut -d':' -f1)
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

## Level 5 → Level 6






## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
## Level 4 → Level 5
