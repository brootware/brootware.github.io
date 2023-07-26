---
layout: post
title: "Mini Memory CTF \U0001F575️ \U0001F4BB"
categories:
- CTF
- Forensics
tags:
- CTF
- Memory-Forensics
- Windows-Forensics
- DFIR
- Blue-team
date: 2022-03-08 20:49 +0800
---

<a><img src="https://img.shields.io/badge/completed-mini%20memory%20ctf-brightgreen" /></a>

## Category

Forensics

## Challenge Details

This Mini Memory CTF contest has ended, but you can still play! This is an excellent opportunity to get some hands-on practice with memory forensics. You'll find the questions below, as well as a link to download the memory sample needed to answer those questions. When you've completed the challenge, download the solutions guide to check your work.

👉 Memory Sample
<https://drive.google.com/drive/folders/1E-i2RTUBXBGUd_Xz0k67kFOpHcr6WX8J>

👉 Mini Memory CTF Solutions Guide
<https://www.13cubed.com/downloads/mini_memory_ctf_solutions_guide.pdf>

## Solution

### Question #1

Find the running rogue (malicious) process. The flag is the MD5 hash of its PID.

First we will find out the profile info of the memory dump using imageinfo.

```bash
$ vol.py -f memdump.mem imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...


          Suggested Profile(s) : Win10x64_17134, Win10x64_14393, Win10x64_10586, Win10x64_16299, Win2016x64_14393, Win10x64_17763, Win10x64_15063 (Instantiated with Win10x64_15063)
                     AS Layer1 : SkipDuplicatesAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/cases/minictf/memdump.mem)
                      PAE type : No PAE
                           DTB : 0x1ad002L
                          KDBG : 0xf800ca7bd520L
          Number of Processors : 1
     Image Type (Service Pack) : 0
                KPCR for CPU 0 : 0xfffff800c9714000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2018-08-06 18:13:42 UTC+0000
     Image local date and time : 2018-08-06 14:13:42 -0400
```

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 psscan | grep svchost.exe
Volatility Foundation Volatility Framework 2.6.1
Offset(P)          Name                PID   PPID PDB                Time created                   Time exited                   
------------------ ---------------- ------ ------ ------------------ ------------------------------ ------------------------------

0x0000a780001d6080 svchost.exe        5048    804 0x000000003c400002 2018-08-01 19:21:00 UTC+0000                                 
0x0000c20c6a5514c0 svchost.exe        8808    804 0x0000000079000002 2018-08-06 18:12:05 UTC+0000                                 
0x0000c20c6aa0d580 svchost.exe        8052    804 0x00000000a5c09002 2018-08-06 18:12:40 UTC+0000                                 
0x0000c20c6aaf9080 svchost.exe        1992    804 0x0000000006500002 2018-08-06 18:12:01 UTC+0000                                 
0x0000c20c6ab2b580 svchost.exe.ex     6176   4824 0x000000004d100002 2018-08-01 19:52:19 UTC+0000   2018-08-01 19:52:19 UTC+0000  
0x0000c20c6ab70080 svchost.exe        8852   4824 0x0000000096f00002 2018-08-01 19:59:49 UTC+0000   2018-08-01 20:00:08 UTC+0000  
0x0000c20c6b4c6080 svchost.exe        5048    804 0x000000003c400002 2018-08-01 19:21:00 UTC+0000                                 
0x0000c20c6b513580 svchost.exe        5264    804 0x00000000b8950002 2018-08-01 19:21:11 UTC+0000                                 
0x0000c20c6b585580 svchost.exe        3224    804 0x0000000078e00002 2018-08-01 19:43:30 UTC+0000                                 
0x0000c20c6b5b6580 svchost.exe        4040    804 0x00000000b9770002 2018-08-01 19:21:04 UTC+0000                                 
0x0000c20c6b6a5580 svchost.exe        2020    804 0x0000000023b00002 2018-08-01 19:20:54 UTC+0000                                 
0x0000c20c6b6b5580 svchost.exe        4304    804 0x000000002d400002 2018-08-01 19:20:55 UTC+0000                                 
0x0000c20c6b6c3580 svchost.exe        4132    804 0x0000000028b00002 2018-08-01 19:20:54 UTC+0000                                 
0x0000c20c6b8dd580 svchost.exe         924    804 0x000000010e410002 2018-08-01 19:20:28 UTC+0000                                 
0x0000c20c6b8df580 svchost.exe         904    804 0x000000010ba10002 2018-08-01 19:20:28 UTC+0000                                 
```

Most Parent Process ID has 804 but there are some with sketchy extensions and 4824. We can cross reference by grepping for process id with 4824

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 psscan | grep 4824
Volatility Foundation Volatility Framework 2.6.1
0x0000c20c69cfe580 explorer.exe       4824   4756 0x0000000035800002 2018-08-01 19:20:58 UTC+0000                                 
0x0000c20c6a959580 FTK Imager.exe     3328   4824 0x000000005dd00002 2018-08-06 18:13:14 UTC+0000                                 
0x0000c20c6ab2b580 svchost.exe.ex     6176   4824 0x000000004d100002 2018-08-01 19:52:19 UTC+0000   2018-08-01 19:52:19 UTC+0000  
0x0000c20c6ab70080 svchost.exe        8852   4824 0x0000000096f00002 2018-08-01 19:59:49 UTC+0000   2018-08-01 20:00:08 UTC+0000  
0x0000c20c6ab92580 ByteCodeGenera     6532   4824 0x000000004c200002 2018-08-01 19:50:42 UTC+0000   2018-08-01 19:50:42 UTC+0000  
0x0000c20c6abeb580 notepad.exe        1412   4824 0x0000000056000002 2018-08-06 18:12:15 UTC+0000   2018-08-06 18:12:17 UTC+0000  
0x0000c20c6b588580 ie4uinit.exe       5716   4824 0x00000000bc500002 2018-08-01 19:21:30 UTC+0000   2018-08-01 19:21:31 UTC+0000  
0x0000c20c6c095580 MSASCuiL.exe       6268   4824 0x000000009ad00002 2018-08-01 19:21:56 UTC+0000                                 
0x0000c20c6cdf4580 scvhost.exe         360   4824 0x000000006af00002 2018-08-01 19:56:45 UTC+0000   2018-08-06 18:12:03 UTC+0000  
0x0000c20c6cfb1580 OneDrive.exe       2200   4824 0x00000000ba600002 2018-08-01 19:22:10 UTC+0000                                 
0x0000c20c6cfc2580 vmtoolsd.exe       3372   4824 0x0000000097700002 2018-08-01 19:21:56 UTC+0000                                 
0x0000c20c6d0d2080 Bubbles.scr       10204   4824 0x0000000047700002 2018-08-01 19:50:33 UTC+0000   2018-08-01 19:50:38 UTC+0000  
```

As we can see from the scan 4824 belongs to explorer.exe which has nothing to do with svchost.

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 psscan | grep -i svchost | grep 4824
Volatility Foundation Volatility Framework 2.6.1
Offset(P)          Name                PID   PPID PDB                Time created                   Time exited                   
------------------ ---------------- ------ ------ ------------------ ------------------------------ ------------------------------
0x0000c20c6ab2b580 svchost.exe.ex     6176   4824 0x000000004d100002 2018-08-01 19:52:19 UTC+0000   2018-08-01 19:52:19 UTC+0000  
0x0000c20c6ab70080 svchost.exe        8852   4824 0x0000000096f00002 2018-08-01 19:59:49 UTC+0000   2018-08-01 20:00:08 UTC+0000  
0x0000c20c6d5ac340 svchost.exe.ex     5528   4824 0x0000000119400002 2018-08-01 19:52:20 UTC+0000   2018-08-01 19:52:20 UTC+0000  
0x0000c20c6d6fc580 svchost.exe       10012   4824 0x0000000136200002 2018-08-01 19:49:19 UTC+0000   2018-08-01 19:49:19 UTC+0000  
0x0000c20c6d82e080 svchost.exe        1404   4824 0x00000000a0f00002 2018-08-01 19:54:55 UTC+0000   2018-08-01 19:56:35 UTC+0000  
0x0000c20c6d99b580 svchost.exe.ex     8140   4824 0x00000000b8600002 2018-08-01 19:52:16 UTC+0000   2018-08-01 19:52:16 UTC+0000  
0x0000c20c6dbc5340 svchost.exe        7852   4824 0x000000003ff00002 2018-08-01 19:49:21 UTC+0000   2018-08-01 19:49:22 UTC+0000  
0x0000c20c6ddad580 svchost.exe        8560   4824 0x00000000b2200002 2018-08-01 20:13:10 UTC+0000                                 
```

By narrowing down on svchost with PPID 4824 and cross checking on the time exited we can see that there is a svchost process with PID 8560 still running which is most likely the running rogue process.

md5 hash of process id is as below

```bash
echo -n 8560 | md5sum 
bc05ca60f2f0d67d0525f41d1d8f8717  -
```

### Question #2

Find the running rogue (malicious) process and dump its memory to disk. You'll find the 32-character flag within that process's memory.

To dump the memory of the malicious process id, run as below.

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 memdump -p 8560 --dump-dir=.
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Writing svchost.exe [  8560] to 8560.dmp
```

We can look for the flag string by using the below command:

```bash
$ strings 8560.dmp | grep fl | less
dflt
dflt
dfltdflt
                                        "contents": "da391kdasdaadsssssss    t.h.e. fl.ag.is. M2ExOTY5N2YyOTA5NWJjMjg5YTk2ZTQ1MDQ2Nzk2ODA=",
```

The flag string is base64 encoded. the string can be decoded as below which is an md5 hash.

```bash
$ echo "M2ExOTY5N2YyOTA5NWJjMjg5YTk2ZTQ1MDQ2Nzk2ODA=" | base64 -d
3a19697f29095bc289a96e4504679680
```

### Question #3

What is the MAC address of this machine's default gateway? The flag is the MD5 hash of that MAC address in uppercase with dashes (-) as delimiters. Example: 01-00-A4-FB-AF-C2.

First thought was to use netscan plugin to find the network interfaces but the plugin is actually only showing active connections. So after a little cheating and going through the writeup of the CTF, I used the registry dump approach to check the mac address from SOFTWARE hive.

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 dumpregistry --dump-dir=.
$ ll
total 7413664
drwxrwxr-x 2 sansforensics sansforensics       4096 Mar  8 12:11 ./
drwxrwxr-x 4 sansforensics root                4096 Mar  8 11:11 ../
-rw-rw-r-- 1 sansforensics sansforensics  776687616 Mar  8 12:04 8560.dmp
-rw-rw-r-- 1 sansforensics sansforensics 5368709120 Aug  6  2018 memdump.mem
-rw-rw-r-- 1 sansforensics sansforensics 1366877880 Mar  8 11:10 minictf.zip
-rw-rw-r-- 1 sansforensics sansforensics     151552 Mar  8 12:11 registry.0xffffd38985466000.HARDWARE.reg
-rw-rw-r-- 1 sansforensics sansforensics      32768 Mar  8 12:10 registry.0xffffd38985e5a000.BCD.reg
-rw-rw-r-- 1 sansforensics sansforensics   72097792 Mar  8 12:11 registry.0xffffd38985eb3000.SOFTWARE.reg
-rw-rw-r-- 1 sansforensics sansforensics     270336 Mar  8 12:10 registry.0xffffd38986a96000.DEFAULT.reg
-rw-rw-r-- 1 sansforensics sansforensics      32768 Mar  8 12:11 registry.0xffffd38986bba000.SECURITY.reg
-rw-rw-r-- 1 sansforensics sansforensics      40960 Mar  8 12:10 registry.0xffffd38986bc4000.SAM.reg
-rw-rw-r-- 1 sansforensics sansforensics     176128 Mar  8 12:10 registry.0xffffd38986cd0000.NTUSERDAT.reg
-rw-rw-r-- 1 sansforensics sansforensics     319488 Mar  8 12:11 registry.0xffffd38986dc6000.BBI.reg
-rw-rw-r-- 1 sansforensics sansforensics     196608 Mar  8 12:10 registry.0xffffd38986dea000.NTUSERDAT.reg
-rw-rw-r-- 1 sansforensics sansforensics    1093632 Mar  8 12:10 registry.0xffffd389873c1000.ntuserdat.reg
-rw-rw-r-- 1 sansforensics sansforensics    3076096 Mar  8 12:10 registry.0xffffd389873fb000.UsrClassdat.reg
-rw-rw-r-- 1 sansforensics sansforensics     839680 Mar  8 12:10 registry.0xffffd38987c56000.Amcachehve.reg
-rw-rw-r-- 1 sansforensics sansforensics     114688 Mar  8 12:11 registry.0xffffd389892e2000.ActivationStoredat.reg
-rw-rw-r-- 1 sansforensics sansforensics     339968 Mar  8 12:11 registry.0xffffd389893e4000.ActivationStoredat.reg
-rw-rw-r-- 1 sansforensics sansforensics       8192 Mar  8 12:11 registry.0xffffd38989490000.settingsdat.reg
-rw-rw-r-- 1 sansforensics sansforensics      49152 Mar  8 12:11 registry.0xffffd389894a0000.settingsdat.reg
-rw-rw-r-- 1 sansforensics sansforensics     188416 Mar  8 12:11 registry.0xffffd3898a6e6000.dosvcStatedat.reg
-rw-rw-r-- 1 sansforensics sansforensics      28672 Mar  8 12:10 registry.0xffffd3898c555000.settingsdat.reg
-rw-rw-r-- 1 sansforensics sansforensics      28672 Mar  8 12:10 registry.0xffffd3898ca19000.ActivationStoredat.reg
-rw-rw-r-- 1 sansforensics sansforensics     200704 Mar  8 12:11 registry.0xffffd3898e2c7000.ActivationStoredat.reg
-rw-rw-r-- 1 sansforensics sansforensics       8192 Mar  8 12:11 registry.0xffffd3898e336000.settingsdat.reg
```

We can then use the regripper to dump the registry settings as below.

```bash
rip.pl -r registry.0xffffd38985eb3000.SOFTWARE.reg -f software > out
```

However if you are running the rip.pl in sansforensics workstation, you will run into an error saying `Global symbol "$plugindir" requires explicit package name`. I searched around to solve that issue and come across [a blog that addresses this issue](https://obscurite.hateblo.jp/entry/2022/02/28/003408).

After resolving and searching for Mac address as below, I was able to get the md5 hash of the address.

```bash
$ cat out | grep "DefaultGatewayMac"
  DefaultGatewayMac: 00-50-56-FE-D8-07
```

```bash
$ echo -n "00-50-56-FE-D8-07" | md5sum
6496d43b622a2ad241b4d08699320f4e  -
```

### Question #4

Find the full path of the browser cache created when an analyst visited "www.13cubed.com." The path will begin with "Users\." Convert the path to uppercase. The flag is the MD5 hash of that string.

The question is quite tough and again had to use some help from the writeup to find out about the mftparser plugin which scans and parse entries in the Windows NTFS Master File Table (MFT).

```bash
$ vol.py -f memdump.mem --profile Win10x64_17134 mftparser > ntfspath
Volatility Foundation Volatility Framework 2.6.1

WARNING : volatility.debug    : NoneObject as string: Unable to read 1 bytes from 1024
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 9
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 10
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 11
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 12
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 13
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 14
WARNING : volatility.debug    : NoneObject as string: Array BirthDomainID invalid member 15
```

We can search for 13cubed term as below from the mftparser output.

```bash
$ cat ntfspath | grep 13cubed
2018-08-01 19:29:27 UTC+0000 2018-08-01 19:29:27 UTC+0000   2018-08-01 19:29:27 UTC+0000   2018-08-01 19:29:27 UTC+0000   Users\CTF\AppData\Local\Packages\MICROS~1.MIC\AC\#!001\MICROS~1\Cache\AHF2COV9\13cubed[1].htm
```

Since the question wants the path to be in all upper case, below bash commands are run to convert and get the md5 hash of the path.

```bash
$ cubepath='Users\CTF\AppData\Local\Packages\MICROS~1.MIC\AC\#!001\MICROS~1\Cache\AHF2COV9\13cubed[1].htm'

$ capped=${cubepath^^}

$ echo $capped
USERS\CTF\APPDATA\LOCAL\PACKAGES\MICROS~1.MIC\AC\#!001\MICROS~1\CACHE\AHF2COV9\13CUBED[1].HTM
```

```bash
$ echo -n $capped | md5sum
b5bdd048030cd26ab2d0e7f7e351224d  -
```

## Helpful sources

<https://obscurite.hateblo.jp/entry/2022/02/28/003408>
<https://downloads.volatilityfoundation.org/releases/2.4/CheatSheet_v2.4.pdf>
