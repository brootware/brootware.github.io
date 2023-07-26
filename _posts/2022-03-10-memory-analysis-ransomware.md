---
layout: post
title: Memory Analysis - Ransomware 👾💻☠️
categories:
- CTF
- Forensics
tags:
- CTF
- DFIR
- Blue-team
- Windows-Forensics
date: 2022-03-10 20:10 +0800
---
<a href="https://blueteamlabs.online/achievement/share/challenge/12792/1"><img src="https://img.shields.io/badge/completed-Memory%20Analysis%20--%20Ransomware-brightgreen" /></a>

## Category

Forensics

## Challenge Details

The Account Executive called the SOC earlier and sounds very frustrated and angry. He stated he can’t access any files on his computer and keeps receiving a pop-up stating that his files have been encrypted. You disconnected the computer from the network and extracted the memory dump of his machine and started analyzing it with Volatility. Continue your investigation to uncover how the ransomware works and how to stop it!

## Solution

### Question 1

Run “vol.py -f infected.vmem --profile=Win7SP1x86 psscan” that will list all processes. What is the name of the suspicious process?

**@WannaDecryptor**

```bash
vol.py -f infected.vmem --profile=Win7SP1x86 psscan 
Volatility Foundation Volatility Framework 2.6.1
Offset(P)          Name                PID   PPID PDB        Time created                   Time exited                   
------------------ ---------------- ------ ------ ---------- ------------------------------ ------------------------------
0x000000001ef9ed40 @WanaDecryptor     2688   2732 0x1e6d9460 2021-01-31 18:24:49 UTC+0000   2021-01-31 18:24:49 UTC+0000  
0x000000001efb5418 smss.exe            268      4 0x1e6d9020 2021-01-31 18:01:10 UTC+0000                                 
0x000000001efc1d40 SearchIndexer.     2232    496 0x1e6d9260 2021-01-31 18:01:18 UTC+0000                                 
0x000000001fcbc0f0 sppsvc.exe         2432    496 0x1e6d9580 2021-01-31 18:03:14 UTC+0000                                 
0x000000001fcc6800 @WanaDecryptor     3968   2732 0x1e6d95c0 2021-01-31 18:02:48 UTC+0000                                 
0x000000001fcd4350 or4qtckT.exe       2732   1456 0x1e6d94c0 2021-01-31 18:02:16 UTC+0000                                 
0x000000001fff1c40 System                4      0 0x00185000 2021-01-31 20:56:12 UTC+0000                                 
0x000000001fff6920 System                4      0 0x00185000 2021-01-31 18:01:10 UTC+0000  
```

### Question 2

What is the parent process ID for the suspicious process?

From previous output of psscan.
**2732**

### Question 3

What is the initial malicious executable that created this process?

**or4qtckT.exe**

Using procdump plugin we can dump the executable of process id 2732 as below

```bash
vol.py -f infected.vmem --profile=Win7SP1x86 procdump -D . -p 2732
Volatility Foundation Volatility Framework 2.6.1
Process(V) ImageBase  Name                 Result
---------- ---------- -------------------- ------
0x83ed4350 0x00400000 or4qtckT.exe         OK: executable.2732.exe
```

### Question 4

If you drill down on the suspicious PID (vol.py -f infected.vmem --profile=Win7SP1x86 psscan | grep (PIDhere)), find the process used to delete files

**taskdl.exe**

```bash
vol.py -f infected.vmem --profile=Win7SP1x86 psscan | grep 2732
Volatility Foundation Volatility Framework 2.6.1
0x000000001e992a88 taskdl.exe         4060   2732 0x1e6d9540 2021-01-31 18:24:54 UTC+0000   2021-01-31 18:24:54 UTC+0000  
0x000000001ef9ed40 @WanaDecryptor     2688   2732 0x1e6d9460 2021-01-31 18:24:49 UTC+0000   2021-01-31 18:24:49 UTC+0000  
0x000000001fcc6800 @WanaDecryptor     3968   2732 0x1e6d95c0 2021-01-31 18:02:48 UTC+0000                                 
0x000000001fcd4350 or4qtckT.exe       2732   1456 0x1e6d94c0 2021-01-31 18:02:16 UTC+0000
```

### Question 5

Find the path where the malicious file was first executed

> C:\Users\hacker\Desktop\or4qtckT.exe

Using cmdline plugin, we can grep the path with process ID.

```bash
vol.py -f infected.vmem --profile=Win7SP1x86 cmdline | grep -A 4 2732
Volatility Foundation Volatility Framework 2.6.1
or4qtckT.exe pid:   2732
Command line : "C:\Users\hacker\Desktop\or4qtckT.exe" 
************************************************************************
taskhsvc.exe pid:   2968
Command line : TaskData\Tor\taskhsvc.exe
```

### Question 6

Can you identify what ransomware it is? (Do your research!)

**WannaCry**

Get the hash of the dumped executable file and search on virus total.
<https://www.virustotal.com/gui/file/5215d03bf5b6db206a3da5dde0a6cbefc8b4fee2f84b99109b0fce07bd2246d6/detection>

```bash
sha256sum executable.2732.exe 
5215d03bf5b6db206a3da5dde0a6cbefc8b4fee2f84b99109b0fce07bd2246d6  executable.2732.exe
```

### Question 7

What is the filename for the file with the ransomware public key that was used to encrypt the private key? (.eky extension)

**00000000.eky**

```bash
vol.py -f infected.vmem --profile=Win7SP1x86 memdump -p 2732 --dump-dir .
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Writing or4qtckT.exe [  2732] to 2732.dmp
```

```bash
strings 2732.dmp | grep .eky
%08X.eky
%08X.eky
00000000.eky
```
