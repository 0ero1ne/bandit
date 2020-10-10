# Bandit - OverTheWire.com

This is a guide to complete the Bandit machine.

[Bandit Machine](https://overthewire.org/wargames/bandit/)

To connect to the box you need **ssh** installed.  
If you have read the introduction to the machine we can start.

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


