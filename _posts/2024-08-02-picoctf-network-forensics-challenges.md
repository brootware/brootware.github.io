---
layout: post
title: Picoctf - Network Forensics Challenges
categories:
- CTF
- Forensics
tags:
- CTF
- Forensics
date: 2024-08-02 17:37 +0800
---
## PcapPoisoning

### Challenge

How about some hide and seek heh? Download the pcap file attached and find the flag.

### Solution

The challenge is very simple if you know your way around wireshark. I have recently gone through a wireshark fundamental course by Chris Greer from Experts with David Bombal and learned some wireshark filtering skills.

To get to the solution, wireshark has a fuzzy finding filter called `frame matches "your_string_here"`. This enables user to find any string matching the pattern regardless of case sensitivity. With that we can either use wireshark GUI or tshark CLI to grep the flag.

On tshark CLI:

```bash
tshark -n -r trace.pcap -Y 'frame matches "pico"' -x | grep -i picoCTF -A 3

0020  50 02 20 00 b0 19 00 00 70 69 63 6f 43 54 46 7b   P. .....picoCTF{
0030  50 36 34 50 5f 34 4e 34 4c 37 53 31 53 5f 53 55   P64P_4N4L7S1S_SU
0040  35 35 33 35 35 46 55 4c 5f 66 36 32 31 66 61 33   55355FUL_f621fa3
0050  37 7d                                             7}
```

## FindAndOpen

### Challenge

Someone might have hidden the password in the trace file. Find the key to unlock this file. This tracefile might be good to analyze.

### Solution

We are given 2 files: 1 pcap file to analyze and find the password and 1 encrypted zip file to the second part of the flag.

First off, we can analyze the pcap file by using wireshark and go through the packets. (I would include screenshots but lazy to upload them to link back to this write up.) Alternatively, you could also use the tshark command below to dump the packets and go through them.

```bash
tshark -n -r dump.pcap -x
```

Something peculiar stood out to me as base64 string in Data payload of packet 48.

```bash
tshark -n -r dump.pcap -Y "frame.number == 48" -V
Frame 48: 70 bytes on wire (560 bits), 70 bytes captured (560 bits)
    Encapsulation type: Ethernet (1)
    Arrival Time: Oct 22, 2022 15:33:33.157638000 +08
    UTC Arrival Time: Oct 22, 2022 07:33:33.157638000 UTC
    Epoch Arrival Time: 1666424013.157638000
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 9.165145000 seconds]
    [Time delta from previous displayed frame: 0.000000000 seconds]
    [Time since reference or first frame: 24.240874000 seconds]
    Frame Number: 48
    Frame Length: 70 bytes (560 bits)
    Capture Length: 70 bytes (560 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: eth:ethertype:data]
Ethernet II, Src: 50:4a:47:54:46:52, Dst: 41:41:42:42:48:48
    Destination: 41:41:42:42:48:48
        Address: 41:41:42:42:48:48
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...1 .... .... .... .... = IG bit: Group address (multicast/broadcast)
    Source: 50:4a:47:54:46:52
        Address: 50:4a:47:54:46:52
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Type: Unknown (0x4c4b)
Data (56 bytes)

0000  56 47 68 70 63 79 42 70 63 79 42 30 61 47 55 67   VGhpcyBpcyB0aGUg
0010  63 32 56 6a 63 6d 56 30 4f 69 42 77 61 57 4e 76   c2VjcmV0OiBwaWNv
0020  51 31 52 47 65 31 49 7a 4e 45 52 4a 54 6b 64 66   Q1RGe1IzNERJTkdf
0030  54 45 39 4c 5a 46 38 3d                           TE9LZF8=
    Data: 564768706379427063794230614755676332566a636d56304f69427761574e76513152476531497a4e45524a546b64665445394c5a46383d
    [Length: 56]
```

Grab the hex values of the data in the packet and decode into ascii

```bash
echo -n '564768706379427063794230614755676332566a636d56304f69427761574e76513152476531497a4e45524a546b64665445394c5a46383d' | xxd -r -p
VGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8=
```

Further decode with base64 to get the first part of the flag

```bash
echo -n '564768706379427063794230614755676332566a636d56304f69427761574e76513152476531497a4e45524a546b64665445394c5a46383d' | xxd -r -p | base64 -d
This is the secret: picoCTF{R34DING_LOKd_
```

Use the secret to unlock the zip file

```bash
➜  Downloads unzip flag.zip
Archive:  flag.zip
[flag.zip] flag password:
 extracting: flag
➜  Downloads cat flag
picoCTF{R34DING_LOKd_fil56_succ3ss_419835ef}
```