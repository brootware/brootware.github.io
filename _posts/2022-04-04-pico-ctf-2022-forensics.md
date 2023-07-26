---
layout: post
title: 'Pico CTF 2022: Forensics 🕵️ 🏁'
date: 2022-04-04 11:31 +0800
categories:
- CTF
- Forensics
tags:
- CTF
- Forensics
- DFIR
- Blue-team
- picoctf-2022
---

## Enhance

Download this image file and find the flag.

```bash
strings drawing.flag.svg
picoCTF{3nh4nc3d_58bd3420}
```

Flag : `picoCTF{3nh4nc3d_58bd3420}`

## Lookey here

Attackers have hidden information in a very large mass of data in the past, maybe they are still doing it.

```bash
grep 'pico' anthem.flag.txt 
      we think that the men of picoCTF{gr3p_15_@w3s0m3_429334b2}
```

Flag : `picoCTF{gr3p_15_@w3s0m3_429334b2}`

## File types

This file was found among some files marked confidential but my pdf reader cannot read it, maybe yours can.

```bash
file Flag.pdf 
Flag.pdf: shell archive text

mv Flag.pdf Flag.sh
chmod +x Flag.sh
/Flag.sh
x - created lock directory _sh00047.
x - extracting flag (text)
x - removed lock directory _sh00047.
ll
-rw-r--r-- 1 sansforensics sansforensics 1.1K Mar 15 06:50 flag
-rwxrwxr-x 1 sansforensics sansforensics 5.1K Mar 15 06:50 Flag.sh
file flag
flag: current ar archive
binwalk flag

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
100           0x64            bzip2 compressed data, block size = 900k

binwalk --dd='.*' flag
➜  forensics ls
anthem.flag.txt  drawing.svg  flag  _flag.extracted  Flag.sh
➜  forensics cd _flag.extracted 
➜  _flag.extracted ls
64
➜  _flag.extracted file 64 
64: bzip2 compressed data, block size = 900k
➜  _flag.extracted mv 64 file.bzip2
➜  _flag.extracted bzip2 -d file.bzip2
bzip2: Can't guess original name for file.bzip2 -- using file.bzip2.out

bzip2: file.bzip2: trailing garbage after EOF ignored
➜  _flag.extracted ls
file.bzip2.out
➜  _flag.extracted file file.bzip2.out 
file.bzip2.out: gzip compressed data, was "flag", last modified: Tue Mar 15 06:50:46 2022, from Unix, original size modulo 2^32 330
➜  _flag.extracted mv file.bzip2.out file.gz
➜  _flag.extracted gzip -d file.gz 
➜  _flag.extracted ls
file
➜  _flag.extracted file file
file: lzip compressed data, version: 1
➜  _flag.extracted mv file file.lz
➜  _flag.extracted lzip -d file.lz 
zsh: command not found: lzip
➜  _flag.extracted lzip -d file.lz
➜  _flag.extracted ls
file
➜  _flag.extracted file file
file: LZ4 compressed data (v1.4+)
➜  _flag.extracted mv file file.lz4
➜  _flag.extracted lz4 -d file.lz4 
Decoding file file 
                                                                          file.lz4             : decoded 266 bytes 
➜  _flag.extracted ls
file  file.lz4
➜  _flag.extracted file file
file: LZMA compressed data, non-streamed, size 255
➜  _flag.extracted mv file file.lzma
➜  _flag.extracted lzma -d file.lzma 
➜  _flag.extracted ls
file  file.lz4
➜  _flag.extracted file file
file: lzop compressed data - version 1.040, LZO1X-1, os: Unix
➜  _flag.extracted mv file file.lzop
➜  _flag.extracted lzop -d file.lz
lzop: file.lz: can't open input file: No such file or directory
➜  _flag.extracted lzop -d file.lzop 
➜  _flag.extracted ls
file  file.lz4  file.lzop
➜  _flag.extracted file file
file: lzip compressed data, version: 1
➜  _flag.extracted mv file file.lzip
➜  _flag.extracted lzip -d file.lzip
➜  _flag.extracted ls
file.lz4  file.lzip.out  file.lzop
➜  _flag.extracted file file.lzip.out 
file.lzip.out: XZ compressed data
➜  _flag.extracted rm file.lz4
➜  _flag.extracted rm file.lzop
➜  _flag.extracted ls
file.lzip.out
➜  _flag.extracted file file.lzip.out 
file.lzip.out: XZ compressed data
➜  _flag.extracted mv file.lzip.out file.xz
➜  _flag.extracted xz -d file.xz 
➜  _flag.extracted ls
file
➜  _flag.extracted file file
file: ASCII text
➜  _flag.extracted cat file 
7069636f4354467b66316c656e406d335f6d406e3170756c407431306e5f
6630725f3062326375723137795f35613833373565307d0a
➜  _flag.extracted echo '7069636f4354467b66316c656e406d335f6d406e3170756c407431306e5f
6630725f3062326375723137795f35613833373565307d0a' | xxd -r -p
picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_5a8375e0}
```

Flag: `picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_5a8375e0}`

## Packets Primer

Download the packet capture file and use packet analysis software to find the flag.

```bash
strings network-dump.flag.pcap
k&Nar
n#('
k&Na
k&Na`
n#('
k&Na;
n#('
p i c o C T F { p 4 c k 3 7 _5 h 4 r k_ 7 d 3 2 b 1 d e }
k&Naa
ep&Na(
p&NaX
p&Na28
p&Na
```

Flag : `picoCTF{p4ck37_5h4rk_7d32b1de}`

## Redaction gone wrong

Now you DON’T see me.
This report has some critical data in it, some of which have been redacted correctly, while some were not. Can you find an important key that was not redacted properly?

Use wondershare pdf to move the redacted portions as per screenshot below.

![image](https://bn1304files.storage.live.com/y4m4t6cEqcHGy2WTGrLAU8mK0nELR3ymhEt7hNixDuJzyuvBlzcAG2T_ygUPB9Nj5rhw1g76QQXcPkTMaK8t9FaYtQT2RCI8P07fzu2UQKXiUm54n53n9epf__DSj3tAwP4_r-2-xM6oUVrLaMB0pQM_Id5ndNRgp8VyU-pgwEQysP3PwER2PwEs1W9mo5R49av?width=2876&height=1334&cropmode=none)

Flag : `picoCTF{C4n_Y0u_S33_m3_fully}`

## sleuthkit intro

Download the disk image and use mmls on it to find the size of the Linux partition. Connect to the remote checker service to check your answer and get the flag.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.

```bash
gzip -d disk.img.gz
mmls disk.img 

DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000204799   0000202752   Linux (0x83)

nc saturn.picoctf.net 52279

What is the size of the Linux partition in the given disk image?
Length in sectors: 0000202752
0000202752
Great work!
picoCTF{mm15_f7w!}
```

Flag : `picoCTF{mm15_f7w!}`

## sleuthkit apprentice

Download this disk image and find the flag.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.

Open the disk image in autopsy. Navigate to partition 3 path as below.

Contents Of File: /3/root/.ash_history

```bash
apk add nano
mkdir my_folder
cd my_folder/
nano flag.txt
ls -al
iconv -f ascii -t utf16 > flag.uni.txt
l
ls -al
iconv -f ascii -t utf16 flag.txt > flag.uni.txt
ls -al
shred
shred -zu flag.txt 
ls -al
halt
```

From the bash history, we can see that the flag is obfuscated in `my_folder/` directory. You can use a text editor to replace the `�` characters.
Contents Of File: /3/root/my_folder/flag.uni.txt

```txt
�p�i�c�o�C�T�F�{�b�y�7�3�_�5�u�r�f�3�r�_�4�2�0�2�8�1�2�0�}�
picoCTF{by73_5urf3r_42028120}
```

Flag : `picoCTF{by73_5urf3r_42028120}`

## Eavesdrop

Download this packet capture and find the flag.

Using wireshark, you can read the insecure conversation as below.

```txt
Hey, how do you decrypt this file again?
You're serious?
Yeah, I'm serious
*sigh* openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
Ok, great, thanks.
Let's use Discord next time, it's more secure.
C'mon, no one knows we use this program like this!
Whatever.
Hey.
Yeah?
Could you transfer the file to me again?
Oh great. Ok, over 9002?
Yeah, listening.
Sent it
Got it.
You're unbelievable
```

You can filter the wireshark results to port 9002 to find the tcp payload in hex as per screenshot.

![wireshark](https://bn1304files.storage.live.com/y4mgcTZrEC1ea0QZs9JqWqG255WrvWnUUeeDECk_7z_F_0US4OHLQpiytyXA4VWSI92yAzQmGg2mPIvcMt_oyb2nxCd3c0fNz6N1LzP2uugw6VZSqZRUJzX2XKJZE_hdpM_77Jt4NoghnGMruBnf_uPQK9D1Q0X-MwoDr1ZRBab2DUQUhHXMFz279HSktnkCHWr?width=2744&height=1678&cropmode=none)

once you get the hex, you can dump the file as below and decrypt the file to get the flag.

```bash
➜  salt echo '53616c7465645f5f03a915e72c0fb75f352ada1e0731570d636caf9b67ac264802625a9448b654d1ce8afba4dcae8707' | xxd -r -p > data
➜  salt ls
data
➜  salt file data
data: openssl enc'd data with salted password
➜  salt openssl des3 -d -salt -in data -out file.txt -k supersecretpassword123
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
➜  salt ls
data  file.txt  salted  salt.pcap
➜  salt cat file.txt
picoCTF{nc_73115_411_77b05957}
```

Flag : `picoCTF{nc_73115_411_77b05957}`

## Operation-Oni

Download this disk image, find the key and log into the remote machine.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.

Open the disk using autopsy. Find the ssh file from the disk and connect.

Contents Of File: /2/root/.ssh/id_ed25519

```bash
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBgrXe4bKNhOzkCLWOmk4zDMimW9RVZngX51Y8h3BmKLAAAAJgxpYKDMaWC
gwAAAAtzc2gtZWQyNTUxOQAAACBgrXe4bKNhOzkCLWOmk4zDMimW9RVZngX51Y8h3BmKLA
AAAECItu0F8DIjWxTp+KeMDvX1lQwYtUvP2SfSVOfMOChxYGCtd7hso2E7OQItY6aTjMMy
KZb1FVmeBfnVjyHcGYosAAAADnJvb3RAbG9jYWxob3N0AQIDBAUGBw==
-----END OPENSSH PRIVATE KEY-----
```

Contents Of File: /2/root/.ssh/id_ed25519.pub

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGCtd7hso2E7OQItY6aTjMMyKZb1FVmeBfnVjyHcGYos root@localhost
```

```bash
➜  operationoni vi id_ed25519
➜  operationoni chmod 400 id_ed25519 
➜  operationoni ssh -i id_ed25519 -p 57976 ctf-player@saturn.picoctf.net 
The authenticity of host '[saturn.picoctf.net]:57976 ([18.217.86.78]:57976)' can't be established.
ECDSA key fingerprint is SHA256:0L/+wJ14/Sk4s6Ue+TxXnAW7qNBuaMeIxA9dXp2zzaU.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[saturn.picoctf.net]:57976,[18.217.86.78]:57976' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.13.0-1017-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ctf-player@challenge:~$ ls
flag.txt
ctf-player@challenge:~$ cat flag.txt
picoCTF{k3y_5l3u7h_af277f77}
ctf-player@challenge:~$
```

Flag : `picoCTF{k3y_5l3u7h_af277f77}`

## Operation Orchid

Download this disk image and find the flag.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.

Open the disk file in autopsy.

Contents Of File: /3/root/.ash_history

```bash
touch flag.txt
nano flag.txt 
apk get nano
apk --help
apk add nano
nano flag.txt 
openssl
openssl aes256 -salt -in flag.txt -out flag.txt.enc -k unbreakablepassword1234567
shred -u flag.txt
ls -al
halt
```

Contents Of File: /3/root/flag.txt

```txt
           -0.881573            34.311733
```

Contents Of File: /3/root/flag.txt.enc

![contentsofflag](https://onedrive.live.com/embed?resid=3AC474C28157D633%21343054&authkey=%21AJSEe49FdZPdBuU&width=696&height=89)

Export the flag.txt.enc from autopsy and decrypt the file as below.

```bash
➜  operationorchid ls
disk.flag.img  flag.txt.enc
➜  operationorchid openssl aes256 -d -salt -in flag.txt.enc -out flag.txt -k unbreakablepassword1234567
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
bad decrypt
140318892737856:error:06065064:digital envelope routines:EVP_DecryptFinal_ex:bad decrypt:../crypto/evp/evp_enc.c:610:
➜  operationorchid ls
disk.flag.img  flag.txt  flag.txt.enc
➜  operationorchid cat flag.txt
picoCTF{h4un71ng_p457_17237fce}
```
