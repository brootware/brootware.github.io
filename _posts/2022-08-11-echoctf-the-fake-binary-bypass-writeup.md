---
layout: post
title: "EchoCTF: The Fake Binary Bypass Writeup ⚙️ \U0001F6E0"
categories:
- CTF
- Binary-Exploitation
tags:
- CTF
- Binary-Exploitation
date: 2022-08-11 19:37 +0800
---
## Summary

It has been a while last I did an online CTF. So this entry is a refresher on my knowledge of binary analysis.

We are given a binary file to analyze and find the flags. The challenges were pretty simple. Simple enumerations on the given binary file would lead you to flags. So let's jump in!

### Ident flag from this binary

Lets start with the easy part, analyze the binary and answer what was the hostname of the system this binary was compiled on?

Very first important thing when it comes to binary analysis challenges is to check if there are any unstripped strings in the binary. You can simply use `strings` command for this.

```bash
$ strings -a binary_analysis_bypass | grep ETSCTF
ETSCTF_%x
ETSCTF_%x%x
diz b1n4ry fil3 w4s compiled on ETSCTF_{redacted}
ETSCTF_{redacted}.{redacted}
ETSCTF
```

From this first enumeration we already got the hostname of the system and the complete source code filename that produced this binary.

### Linux distro and version this binary was compiled on

```bash
$ file binary_analysis_bypass 
binary_analysis_bypass: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, for GNU/Linux 3.2.0, BuildID[sha1]=89047470b27fed12ce5e60b0b1c55e528c2e461f, not stripped
```

From checking out the file type of the binary we can see that this is most likely compiled using gnu compiler. We can try to look for `gcc` in the strings output of the binary to try to find out linux distro and version.

```bash
$ strings -a binary_analysis_bypass | grep gcc   
DW.ref.__gcc_personality_v0
.gcc_except_table

$ strings binary_analysis_bypass | grep GCC   
GCC: (redacted) 8.3.0
```

### Name of the default function being called by main()

When the program is run it executes a single function from its main(). Analyze the binary and answer what is the name of that function.

We will need ghidra to do static code analysis for this binary.
First find out the main function from the ghidra UI as below.

![main](https://bn1304files.storage.live.com/y4msnt64-G42WH27Ngr9HKUBp8XiS0BYuBWNa3dYrHN1UJ3JES8aqD5osvtvvmjudI8pH-R0ZAGAIt0VXUWi2QEW54_4UHXvJ4FYEunBjFmhG-DhQ8aSlVjJC7DsqJNBqfgjnQSeLscTAjerHMRLGsx7sGmt6f6Q7Kf4HPWO-ziP8WdS--Bl9UVEKJHpCYMPS1k?width=492&height=1660&cropmode=none)

Click into the main function and you will be able to see the function that is being called in the main function.

![functioncall](https://bn1304files.storage.live.com/y4mhfkj0Z0rFL4rcXsZCspL2e_8HruaOdt7_wpyoWl804R9i98FRHu83HQ0ZlAsEcx8chPutTwqo3nKiLjOHEZ5dZieRqVbrg7QtTpeH4UZsxOafkhYyiA0x7iaU4t9dmQ_uEtCLsRI-7WFFnikNHCRSW354tsMaQe4A8a_ptaHsUDbNG9fDYhuqHDaFv1LFpkL?width=2858&height=1492&cropmode=none)

What would be the flag being displayed when the program is run?

```bash
$ ./binary_analysis_bypass 
ETSCTF_{redacted}
```

### What is the ETSCTF variable value

There is an ETSCTF variable defined, see if you can get its value in hex (format 0123abcd).

For this challenge, you need to find the variable named ETSCTF in ghidra from the Symbol Tree panel and find out the value that is stored in the variable. Check out the screenshot below on how to do this.

![ETSCTFVAR](https://bn1304files.storage.live.com/y4mUQgBESiS8e0s-B-dEQhDcZcIP_gD-7a7Wd__ok03WZlMoYlYIjJV4nJsRhyL27Gy_QWuE2LF3hKBS0LekmtDv2zAMkFTa48P5FN_ry_DZUMPXgUrcJcqCwPEu10_QXLv0ozDYxM0mp-GEZVKC6fN4QmQjIsbZi1F04ybI9GiYJSZG9mJv8cMXArMbW7A_tOi?width=2858&height=1492&cropmode=none)

### What is the function name that displays an actual flag?

Analyze the binary and answer the name of the function that isn't being called by default?

By analyzing the program flow in ghidra, we can see that there are 2 functions. One is being called by the main function while the other is not. This is the hiddne function that the challenge is asking for.

![hiddnefunc](https://bn1304files.storage.live.com/y4mTrmU3nOXBf7e1RFJctv-hnpqO_qxDCyRIuhOCGiEKRYRuK6QyLpZk4WiJe-4VesIeWHnU5PPJ-oHXByZhX8cHVlMoSQ70DwF6zyu3ccKzCtcrEo5yRVDS_eyvLJqyhEmiuGWAMx7A97tMqTRx7hOVnVwCN51NtFCl3VnUSuX9JKYwOW8ncOXngW63SGzxcTt?width=2858&height=1492&cropmode=none)

### Complete and correct flag that isn't being displayed

By analyzing the function from previous question, we can see that the correct flag is a combination of the variables `m1` and `m2`. You just need to find the values of these variables and concatenate them together to get the correct flag.

![correctflag](https://bn1304files.storage.live.com/y4mlMqY54COTvOa9JFDTxp-GyCzqHsSJB699CHjEETJnwoXCYQAExA9kSM1ahOK0I8-YlhmMNGPaLyBTJP_dfEMa0tN1mjNJDncXtpB2ZMPcRjdusk-lD4e1eSJ8yfSKcJ8lWiwNyvuSgxPKapmUxQqKAvuaQUybN41C7QGLKp6OoYU4nZqTUyu5Be2ImIgRsE8?width=2858&height=1492&cropmode=none)

You can also find the ETSCTF local variable defined within this function from `local_c` from the screenshot.
