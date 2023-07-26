---
layout: post
title: Extensions 🚩
date: 2022-01-15 08:39 +0800
categories: [CTF, Forensics]
tags: [CTF, Forensics]
---

Point: 150

## Category

Forensics

## Question

### This is a really weird text file [TXT](https://jupiter.challenges.picoctf.org/static/e7e5d188621ee705ceeb0452525412ef/flag.txt)? Can you find the flag?

## Hint

How do operating systems know what kind of file it is? (It's not just the ending!

Make sure to submit the flag as picoCTF{XXXXX}

## Solution

This challenge is fairly simple if you know how file extensions work in different operating systems. One line explanation of file extensions is: it is a tag for an operating system to interpret and run the file using their respective programs. E.g: files with pdf extension can be run using a pdf reader.

There is a really good in-depth explanation on this by LiveOverFlow which I highly recommend watching.
<https://youtu.be/VVdmmN0su6E>

With that being said, here's how I approached the problem.

```bash
wget https://jupiter.challenges.picoctf.org/static/e7e5d188621ee705ceeb0452525412ef/flag.txt

file flag.txt
flag.txt: PNG image data, 1697 x 608, 8-bit/color RGB, non-interlaced
```

From here, we can find that the original file format is PNG image data. We can change the extension as below.

```bash
cp flag.txt flag.png

open flag.png
```

The flag can be found using an image viewer.

## Improvement

None

## For more writeups

This write up is featured as just one of many write-ups that has been written. For more writeups please visit this [repo](https://github.com/brootware/CTF-Writeups)
