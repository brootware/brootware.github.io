---
layout: post
title: Memory Forensics Deep Dive
categories: [CTF, Forensics]
tags: [CTF, Memory-Forensics, DFIR, Blue-team]
---
## Category

Forensics

## Challenge Details

You have given a memory image for a compromised machine. Analyze the image and figure out attack details.

## Solution

### 1 What profile should you use for this memory sample?

**Win7SP1x64_24000**

```bash
$ vol.py -f banking-malware.vmem imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/cases/banking/banking-malware.vmem)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80002bef120L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002bf1000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2021-02-09 00:51:25 UTC+0000
     Image local date and time : 2021-02-08 22:51:25 -0200
```

### 2 What is the KDBG virtual address of the memory sample?

**0xf80002bef120**

By looking at the output from previous imageinfo detail. Do note that the L is usually appended after the virtual address as convention. You will need to strip it off to get the correct answer.

### 3 There is a malicious process running, but it's hidden. What's its name?

**vds_ps.exe**

From the output of psxview which scans all the running processes (hidden ones included), we can see that vds_ps.exe shows up as *False* which means the process is hidden from typical pslist plugin.

```bash
$ vol.py -f banking-malware.vmem --profile Win7SP1x64_24000 psxview
Volatility Foundation Volatility Framework 2.6.1
Offset(P)          Name                    PID pslist psscan thrdproc pspcid csrss session deskthrd ExitTime
------------------ -------------------- ------ ------ ------ -------- ------ ----- ------- -------- --------
0x000000007e651b00 services.exe            500 True   True   True     True   True  True    False    
0x000000007d9b4b00 svchost.exe            1772 True   True   True     True   True  True    True     
0x000000007d2e5060 VSSVC.exe              2104 True   True   True     True   True  True    True     
0x000000007da4bb00 lsm.exe                 516 True   True   True     True   True  True    False    
0x000000007d38cb00 vm3dservice.ex         2416 True   True   True     True   True  True    True     
0x000000007d97f940 svchost.exe             356 True   True   True     True   True  True    True     
0x000000007d827710 svchost.exe             636 True   True   True     True   True  True    True     
0x000000007fe47b00 CompatTelRunne          996 True   True   True     True   True  True    True     
0x000000007cf2fb00 WmiPrvSE.exe           3488 True   True   True     True   True  True    True     
0x000000007da0a060 winlogon.exe            480 True   True   True     True   True  True    True     
0x000000007d2735e0 dllhost.exe            2044 True   True   True     True   True  True    True     
0x000000007fcf0b00 sppsvc.exe             3016 True   True   True     True   True  True    True     
0x000000007d8ce920 WmiApSrv.exe           3692 True   True   True     True   True  True    True     
0x000000007d68c6b0 OfficeClickToR         1232 True   True   True     True   True  True    True     
0x000000007d353b00 dwm.exe                2236 True   True   True     True   True  True    True     
0x000000007e770060 SDXHelper.exe          2828 True   True   True     True   True  True    True     
0x000000007db38930 lsass.exe               508 True   True   True     True   True  True    False    
0x000000007d2f3580 dllhost.exe            3188 True   True   True     True   True  True    True     
0x000000007d5d7b00 WmiPrvSE.exe           1812 True   True   True     True   True  True    True     
0x000000007e682b00 taskhost.exe           2344 True   True   True     True   True  True    True     
0x000000007feb8b00 vmtoolsd.exe           1484 True   True   True     True   True  True    True     
0x000000007d24d2f0 SDXHelper.exe          3200 True   True   True     True   True  True    True     
0x000000007e5f45c0 svchost.exe             704 True   True   True     True   True  True    True     
0x000000007d6bab00 VGAuthService.         1432 True   True   True     True   True  True    True     
0x000000007d6dbb00 msdtc.exe              1968 True   True   True     True   True  True    True     
0x000000007ddcf060 svchost.exe             916 True   True   True     True   True  True    True     
0x000000007d336950 vds_ps.exe             2448 False  False  True     True   True  True    True     
0x000000007d2508c0 conhost.exe            3028 True   True   True     True   True  True    True     
0x000000007df7db00 SearchIndexer.         2616 True   True   True     True   True  True    True     
0x000000007d678060 wmpnetwk.exe            856 True   True   True     True   True  True    True     
0x000000007e8906d0 wininit.exe             412 True   True   True     True   True  True    True     
0x000000007d33eb00 taskhost.exe           2192 True   True   True     True   True  True    True     
0x000000007eae4060 CompatTelRunne         2984 True   True   True     True   True  True    True     
0x000000007e1bfb00 svchost.exe            3324 True   True   True     True   True  True    False    
0x000000007d69bb00 svchost.exe            1288 True   True   True     True   True  True    True     
0x000000007d902060 svchost.exe             868 True   True   True     True   True  True    True     
0x000000007d9ff610 svchost.exe            1132 True   True   True     True   True  True    True     
0x000000007d3fc3e0 vmtoolsd.exe           2424 True   True   True     True   True  True    True     
0x000000007d35db00 explorer.exe           2260 True   True   True     True   True  True    True     
0x000000007d203930 dllhost.exe            1832 True   True   True     True   True  True    True     
0x000000007d929370 audiodg.exe            1008 True   True   True     True   True  True    True     
0x000000007db26b00 svchost.exe            3084 True   True   True     True   True  True    True     
0x000000007e462b00 CompatTelRunne         2688 True   True   True     True   True  True    True     
0x000000007d3535a0 taskeng.exe            2244 True   True   True     True   True  True    True     
0x000000007d8a7b00 svchost.exe             756 True   True   True     True   True  True    True     
0x000000007d24da30 SDXHelper.exe          3196 True   True   True     True   True  True    True     
0x000000007d989b00 spoolsv.exe            1096 True   True   True     True   True  True    True     
0x000000007ddc4060 svchost.exe             960 True   True   True     True   True  True    True     
0x000000007fd4a6e0 ipconfig.exe           4008 True   True   False    True   False True    False    2021-02-09 00:51:25 UTC+0000
0x000000007dde39a0 csrss.exe               360 True   True   True     True   False True    True     
0x000000007fc46b00 cmd.exe                3732 True   True   False    True   False True    False    2021-02-09 00:51:25 UTC+0000
0x000000007ee7d6c0 smss.exe                272 True   True   True     True   False False   False    
0x000000007e9c6060 csrss.exe               424 True   True   True     True   False True    True     
0x000000007ffad860 System                    4 True   True   True     True   False False   False    
0x000000007fc52060 conhost.exe            3848 True   True   False    True   False True    False    2021-02-09 00:51:25 UTC+0000
```

### 4 What is the physical offset of the malicious process?

**0x000000007d336950**
Using the output from previous psxview command

```console
Offset(P)          Name                    PID pslist psscan thrdproc pspcid csrss session deskthrd ExitTime
------------------ -------------------- ------ ------ ------ -------- ------ ----- ------- -------- --------
0x000000007d336950 vds_ps.exe             2448 False  False  True     True   True  True    True  
```

### 5 What is the full path (including executable name) of the hidden executable?

**C:\Users\john\AppData\Local\api-ms-win-service-management-l2-1-0\vds_ps.exe**

vadinfo is a volatility plugin to find process id. We first try to find the info of the process using process id. However, as this is a memory image and the process is not actually running, we get back the error. The provided error message is helpful in such a way that it asked us to use the physical offset of the malicious process.

```bash
$ vol.py -f banking-malware.vmem --profile Win7SP1x64_24000 vadinfo -p 2448
Volatility Foundation Volatility Framework 2.6.1
ERROR   : volatility.debug    : Cannot find PID 2448. If its terminated or unlinked, use psscan and then supply --offset=OFFSET
```

```bash
$ vol.py -f banking-malware.vmem --profile Win7SP1x64_24000 vadinfo --offset=0x000000007d336950 | grep vds_ps.exe
Volatility Foundation Volatility Framework 2.6.1
FileObject @fffffa80046035d0, Name: \Device\HarddiskVolume1\Users\john\AppData\Local\api-ms-win-service-management-l2-1-0\vds_ps.exe
```

### 6 Which malware is this?

**Emotet**

We will use procdump to extract the executable file from the physical location of the memory address

```bash
$ vol.py -f banking-malware.vmem --profile Win7SP1x64_24000 procdump --offset=0x000000007d336950 -D .
Volatility Foundation Volatility Framework 2.6.1
Process(V)         ImageBase          Name                 Result
------------------ ------------------ -------------------- ------
0xfffffa8004536950 0x0000000000400000 vds_ps.exe           OK: executable.2448.exe
```

After dumping the executable from the memory, we can get hash of the file to search on virus total.

```bash
sha256sum executable.2448.exe 
c5c5e5f6da7ec82875410b971b3f02f09e35fc25fe714441347753d1b7b656ea  executable.2448.exe
```

![emotet](https://bn1304files.storage.live.com/y4mkGAMDlV1zt28mCx7NDx9ufPYgfYQbV-YhWsP2LT_Mq0usMjvmqCAYUU8Y3QDiqq-Rc_6P-ub9l2mlTMGEOMHGs3pAN6CHkXeZzGwqom8cfe5Ry53MleNU0GQC-vrEnIFmgwsGiRh07uuBdR0UaE7aq-qxH2XfyrD51lNEKbRfiSdsE7YnPpjuIUP1A_pLAJo?width=2832&height=1038&cropmode=none)

From virustotal, we can see the original name of the trojan "Emotet"

### 7 The malicious process had two PEs injected into its memory. What's the size in bytes of the Vad that contains the largest injected PE? Answer in hex, like: 0xABC

```bash
$ vol.py -f banking-malware.vmem --profile=Win7SP1x64_24000 malfind  --offset=0x000000007d336950 
Volatility Foundation Volatility Framework 2.6.1
Process: vds_ps.exe Pid: 2448 Address: 0x220000
Vad Tag: VadS Protection: PAGE_EXECUTE_READWRITE
Flags: CommitCharge: 32, MemCommit: 1, PrivateMemory: 1, Protection: 6

0x0000000000220000  e8 00 00 00 00 58 89 c3 05 29 05 00 00 81 c3 29   .....X...).....)
0x0000000000220010  f7 01 00 68 01 00 00 00 68 05 00 00 00 53 68 80   ...h....h....Sh.
0x0000000000220020  7b 1c ed 50 e8 04 00 00 00 83 c4 14 c3 83 ec 48   {..P...........H
0x0000000000220030  83 64 24 18 00 b9 4c 77 26 07 53 55 56 57 33 f6   .d$...Lw&.SUVW3.

0x0000000000220000 e800000000       CALL 0x220005
0x0000000000220005 58               POP EAX
0x0000000000220006 89c3             MOV EBX, EAX
0x0000000000220008 0529050000       ADD EAX, 0x529
0x000000000022000d 81c329f70100     ADD EBX, 0x1f729
0x0000000000220013 6801000000       PUSH DWORD 0x1
0x0000000000220018 6805000000       PUSH DWORD 0x5
0x000000000022001d 53               PUSH EBX
0x000000000022001e 68807b1ced       PUSH DWORD 0xed1c7b80
0x0000000000220023 50               PUSH EAX
0x0000000000220024 e804000000       CALL 0x22002d
0x0000000000220029 83c414           ADD ESP, 0x14
0x000000000022002c c3               RET
0x000000000022002d 83ec48           SUB ESP, 0x48
0x0000000000220030 8364241800       AND DWORD [ESP+0x18], 0x0
0x0000000000220035 b94c772607       MOV ECX, 0x726774c
0x000000000022003a 53               PUSH EBX
0x000000000022003b 55               PUSH EBP
0x000000000022003c 56               PUSH ESI
0x000000000022003d 57               PUSH EDI
0x000000000022003e 33f6             XOR ESI, ESI

Process: vds_ps.exe Pid: 2448 Address: 0x2a10000
Vad Tag: VadS Protection: PAGE_EXECUTE_READWRITE
Flags: CommitCharge: 29, MemCommit: 1, PrivateMemory: 1, Protection: 6

0x0000000002a10000  4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00   MZ..............
0x0000000002a10010  b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00   ........@.......
0x0000000002a10020  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0000000002a10030  00 00 00 00 00 00 00 00 00 00 00 00 b0 00 00 00   ................

0x0000000002a10000 4d               DEC EBP
0x0000000002a10001 5a               POP EDX
0x0000000002a10002 90               NOP
0x0000000002a10003 0003             ADD [EBX], AL
0x0000000002a10005 0000             ADD [EAX], AL
0x0000000002a10007 000400           ADD [EAX+EAX], AL
0x0000000002a1000a 0000             ADD [EAX], AL
0x0000000002a1000c ff               DB 0xff
0x0000000002a1000d ff00             INC DWORD [EAX]
0x0000000002a1000f 00b800000000     ADD [EAX+0x0], BH
0x0000000002a10015 0000             ADD [EAX], AL
0x0000000002a10017 004000           ADD [EAX+0x0], AL
0x0000000002a1001a 0000             ADD [EAX], AL
0x0000000002a1001c 0000             ADD [EAX], AL
0x0000000002a1001e 0000             ADD [EAX], AL
0x0000000002a10020 0000             ADD [EAX], AL
0x0000000002a10022 0000             ADD [EAX], AL
0x0000000002a10024 0000             ADD [EAX], AL
0x0000000002a10026 0000             ADD [EAX], AL
0x0000000002a10028 0000             ADD [EAX], AL
0x0000000002a1002a 0000             ADD [EAX], AL
0x0000000002a1002c 0000             ADD [EAX], AL
0x0000000002a1002e 0000             ADD [EAX], AL
0x0000000002a10030 0000             ADD [EAX], AL
0x0000000002a10032 0000             ADD [EAX], AL
0x0000000002a10034 0000             ADD [EAX], AL
0x0000000002a10036 0000             ADD [EAX], AL
0x0000000002a10038 0000             ADD [EAX], AL
0x0000000002a1003a 0000             ADD [EAX], AL
0x0000000002a1003c b000             MOV AL, 0x0
0x0000000002a1003e 0000             ADD [EAX], AL

Process: vds_ps.exe Pid: 2448 Address: 0x2a80000
Vad Tag: VadS Protection: PAGE_EXECUTE_READWRITE
Flags: CommitCharge: 55, MemCommit: 1, PrivateMemory: 1, Protection: 6

0x0000000002a80000  4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00   MZ..............
0x0000000002a80010  b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00   ........@.......
0x0000000002a80020  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0000000002a80030  00 00 00 00 00 00 00 00 00 00 00 00 c8 00 00 00   ................

0x0000000002a80000 4d               DEC EBP
0x0000000002a80001 5a               POP EDX
0x0000000002a80002 90               NOP
0x0000000002a80003 0003             ADD [EBX], AL
0x0000000002a80005 0000             ADD [EAX], AL
0x0000000002a80007 000400           ADD [EAX+EAX], AL
0x0000000002a8000a 0000             ADD [EAX], AL
0x0000000002a8000c ff               DB 0xff
0x0000000002a8000d ff00             INC DWORD [EAX]
0x0000000002a8000f 00b800000000     ADD [EAX+0x0], BH
0x0000000002a80015 0000             ADD [EAX], AL
0x0000000002a80017 004000           ADD [EAX+0x0], AL
0x0000000002a8001a 0000             ADD [EAX], AL
0x0000000002a8001c 0000             ADD [EAX], AL
0x0000000002a8001e 0000             ADD [EAX], AL
0x0000000002a80020 0000             ADD [EAX], AL
0x0000000002a80022 0000             ADD [EAX], AL
0x0000000002a80024 0000             ADD [EAX], AL
0x0000000002a80026 0000             ADD [EAX], AL
0x0000000002a80028 0000             ADD [EAX], AL
0x0000000002a8002a 0000             ADD [EAX], AL
0x0000000002a8002c 0000             ADD [EAX], AL
0x0000000002a8002e 0000             ADD [EAX], AL
0x0000000002a80030 0000             ADD [EAX], AL
0x0000000002a80032 0000             ADD [EAX], AL
0x0000000002a80034 0000             ADD [EAX], AL
0x0000000002a80036 0000             ADD [EAX], AL
0x0000000002a80038 0000             ADD [EAX], AL
0x0000000002a8003a 0000             ADD [EAX], AL
0x0000000002a8003c c8000000         ENTER 0x0, 0x0
```

```bash
$ vol.py -f banking-malware.vmem --profile=Win7SP1x64_24000 vadinfo  -a 0x2a10000 --offset=0x000000007d336950
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Pid:   2448
VAD node @ 0xfffffa800589cc00 Start 0x0000000002a10000 End 0x0000000002a2cfff Tag VadS
Flags: CommitCharge: 29, MemCommit: 1, PrivateMemory: 1, Protection: 6
Protection: PAGE_EXECUTE_READWRITE
Vad Type: VadNone
```

```bash
$ vol.py -f banking-malware.vmem --profile=Win7SP1x64_24000 vadinfo  -a 0x2a80000 --offset=0x000000007d336950 
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Pid:   2448
VAD node @ 0xfffffa8002f1b640 Start 0x0000000002a80000 End 0x0000000002ab6fff Tag VadS
Flags: CommitCharge: 55, MemCommit: 1, PrivateMemory: 1, Protection: 6
Protection: PAGE_EXECUTE_READWRITE
Vad Type: VadNone
```

```txt
0x0000000002ab6fff − 0x0000000002a80000 = 36fff
0x0000000002a2cfff − 0x0000000002a10000 = 1cfff
```
