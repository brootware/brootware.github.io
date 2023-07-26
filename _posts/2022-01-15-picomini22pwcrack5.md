---
layout: post
title: Beginner picoMini 2022 password crack 5 🚩
date: 2022-01-15 10:22 +0800
categories: [CTF, Reverse-Engineering, Cryptography]
tags: [CTF, Cryptography, Reverse-Engineering]
---

Point: 100

![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)

## Category

Cryptography, Reverse-Engineering

## Question

Can you crack the password to get the flag?
Download the password checker [here](https://artifacts.picoctf.net/c/80/level5.py) and you'll need the encrypted [flag](https://artifacts.picoctf.net/c/80/level5.flag.txt.enc) and the [hash](https://artifacts.picoctf.net/c/80/level5.hash.bin) in the same directory too. Here's a [dictionary](https://artifacts.picoctf.net/c/80/dictionary.txt) with all possible passwords based on the password conventions we've seen so far.

## Hint

Opening a file in Python is crucial to using the provided dictionary.

You may need to trim the whitespace from the dictionary word before hashing. Look up the Python string function, strip.

The str_xor function does not need to be reverse-engineered for this challenge.

## Solution

You are given 4 files. A dictionary file with the list of possible words, an encrypted text file, a hash file and a skeleton python file to reverse engineer the code to crack the password.

First, the password cracker python file is enumerated. The first function can be ignored as it's not part of the challenge as stated.

```python

import hashlib

### THIS FUNCTION WILL NOT HELP YOU FIND THE FLAG --LT ########################
def str_xor(secret, key):
    #extend key to secret length
    new_key = key
    i = 0
    while len(new_key) < len(secret):
        new_key = new_key + key[i]
        i = (i + 1) % len(key)
    return "".join([chr(ord(secret_c) ^ ord(new_key_c)) for (secret_c,new_key_c) in zip(secret,new_key)])
###############################################################################

flag_enc = open('level5.flag.txt.enc', 'rb').read()
correct_pw_hash = open('level5.hash.bin', 'rb').read()

```

The second part is a function that takes in user input and hashes the password with md5.

```python

def hash_pw(pw_str):
    pw_bytes = bytearray()
    pw_bytes.extend(pw_str.encode())
    m = hashlib.md5()
    m.update(pw_bytes)
    return m.digest()

```

This function in the code is the main part we should be paying attention to as we could modify this part to print out the flag.

```python

def level_5_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    user_pw_hash = hash_pw(user_pw)

    if( user_pw_hash == correct_pw_hash ):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), user_pw)
        print(decryption)
        return
    print("That password is incorrect")



level_5_pw_check()

```

The python file is run and as expected, it returns incorrectly.

```bash

➜  pwcrack5 python level5.py
Please enter correct password for flag: hello
That password is incorrect

```

Let's check out the dictionary file. The file is fairly big with over 65k lines of passwords we need to test to correct one.

```bash
➜  pwcrack5 cat dictionary.txt | wc -l
   65536
```

Now let's get into modifying the code to brute force the correct password. A helper function called `grab_pw()` is written to grab all the words, processed and appended into the list to test.

```python

# helper function to grab list of passwords from dictionary.txt
def grab_pw():
    pw_list = []
    with open('dictionary.txt') as word_list:
        for word in word_list:
            # Strip any trailing white spaces and next lines to add into a list.
            pw_list.append(word.strip(' \n'))
    return pw_list

```

The earlier `level_5_pw_check()` is then modified as below to brute force all the values from the `pw_list` to find the correct one and print out the flag.

```python

def level_5_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    # added in malicious code here to brute force
    pw_list = grab_pw()
    for pw in pw_list:
        user_pw = pw
        user_pw_hash = hash_pw(user_pw)

        if(user_pw_hash == correct_pw_hash):
            print("Welcome back... your flag, user:")
            decryption = str_xor(flag_enc.decode(), user_pw)
            print(decryption)
            return
    print("That password is incorrect")

```

Once you run the code after modifications, the flag will be printed out as below.

```bash

➜  pwcrack5 python lvl5solved.py
Please enter correct password for flag: haha pwned by rootware!
Welcome back... your flag, user:
picoCTF{REDACTED}

```

## Improvement

None

## For more writeups

This write up is featured as just one of many write-ups that has been written. For more writeups please visit this [repo](https://github.com/brootware/CTF-Writeups)
