---
layout: post
title: 'Pico CTF 2022: Reverse-Engineering 🛠 💾'
categories:
- CTF
- Reverse-Engineering
tags:
- CTF
- Reverse-Engineering
- picoctf-2022
date: 2022-04-01 11:45 +0800
---
## file-run1

A program has been provided to you, what happens if you try to run it on the command line?
Download the program here.

```bash
➜  revEngineer ls
run
➜  revEngineer file run 
run: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=f9df27b4be0359f544470c9df7db39f209366c6c, for GNU/Linux 3.2.0, not stripped
➜  revEngineer chmod +x run 
➜  revEngineer ./run 
The flag is: picoCTF{U51N6_Y0Ur_F1r57_F113_5578e314}
```

Flag : `picoCTF{U51N6_Y0Ur_F1r57_F113_5578e314}`

## file-run2

Another program, but this time, it seems to want some input. What happens if you try to run it on the command line with input "Hello!"?
Download the program here.

```bash
➜  file-run2 chmod +x run 
➜  file-run2 ./run Hello!
The flag is: picoCTF{F1r57_4rgum3n7_981abfb5}
```

Flag : `picoCTF{F1r57_4rgum3n7_981abfb5}`

## GDB Test Drive

Can you get the flag?
Download this binary.
Here's the test drive instructions:

```bash
$ chmod +x gdbme
$ gdb gdbme
(gdb) layout asm
(gdb) break *(main+99)
(gdb) run
(gdb) jump *(main+104)
```

![image](https://bn1304files.storage.live.com/y4mOnMFNBYYvHqB8_ZnB0U-CeIMG70PQecKXO5ufMCV4vJcyyKBiLmxKWPWVN3lkmsJuV-Ypp7X80i6nHCjNkeWPNihCWOWF-8sWa0ZAiS6JGTr7H6kjQex3hmBXbhSqbtniMHZFAuh8M9d19xdTZaAq35Qc8oXvOFax7dZVH1MynwMbV2dD8rigPUSHW5wS4Wz?width=2750&height=1658&cropmode=none)

```bash
(gdb) break *(main+99)
Breakpoint 1 at 0x132a
(gdb) run
Starting program: /cases/pico2022/revEngineer/gbdtestdrive/gdbme

Breakpoint 1, 0x000055555555532a in main ()
(gdb) jump *(main+104)
Continuing at 0x55555555532f.
picoCTF{d3bugg3r_dr1v3_93b87433}
(gdb) ior 1 (process 23118) exited normally]
```

Flag : `picoCTF{d3bugg3r_dr1v3_93b87433}`

## patchme.py

Can you get the flag?
Run this Python program in the same directory as this encrypted flag.

patchme.flag.py

```python
#### THIS FUNCTION WILL NOT HELP YOU FIND THE FLAG --LT ########################
def str_xor(secret, key):
    #extend key to secret length
    new_key = key
    i = 0
    while len(new_key) < len(secret):
        new_key = new_key + key[i]
        i = (i + 1) % len(key)        
    return "".join([chr(ord(secret_c) ^ ord(new_key_c)) for (secret_c,new_key_c) in zip(secret,new_key)])
###############################################################################


flag_enc = open('flag.txt.enc', 'rb').read()



def level_1_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    if( user_pw == "ak98" + \
                   "-=90" + \
                   "adfjhgj321" + \
                   "sleuth9000"):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), "utilitarian")
        print(decryption)
        return
    print("That password is incorrect")



level_1_pw_check()
```

We simply need to change the if condition as below to decrypt the flag regardless of incorrect password.

```python
def level_1_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    if( user_pw != "ak98" + \
                   "-=90" + \
                   "adfjhgj321" + \
                   "sleuth9000"):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), "utilitarian")
        print(decryption)
        return
    print("That password is incorrect")
```

```bash
python patchme.flag.py 
Please enter correct password for flag: haha
Welcome back... your flag, user:
picoCTF{p47ch1ng_l1f3_h4ck_4d5af99c}
```

Flag : `picoCTF{p47ch1ng_l1f3_h4ck_4d5af99c}`

## Safe Opener

Can you open this safe?
I forgot the key to my safe but this program is supposed to help me with retrieving the lost key. Can you help me unlock my safe?
Put the password you recover into the picoCTF flag format like:
picoCTF{password}

```java
import java.io.*;
import java.util.*;  
public class SafeOpener {
    public static void main(String args[]) throws IOException {
        BufferedReader keyboard = new BufferedReader(new InputStreamReader(System.in));
        Base64.Encoder encoder = Base64.getEncoder();
        String encodedkey = "";
        String key = "";
        int i = 0;
        boolean isOpen;
        

        while (i < 3) {
            System.out.print("Enter password for the safe: ");
            key = keyboard.readLine();

            encodedkey = encoder.encodeToString(key.getBytes());
            System.out.println(encodedkey);
              
            isOpen = openSafe(encodedkey);
            if (!isOpen) {
                System.out.println("You have  " + (2 - i) + " attempt(s) left");
                i++;
                continue;
            }
            break;
        }
    }
    
    public static boolean openSafe(String password) {
        String encodedkey = "cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz";
        
        if (password.equals(encodedkey)) {
            System.out.println("Sesame open");
            return true;
        }
        else {
            System.out.println("Password is incorrect\n");
            return false;
        }
    }
}
```

You're given a java program to reverse engineer the password from. From the program we can see that the password string is encoded in base64. We can simply do as below.

```bash
➜  safeopener echo 'cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz' | base64 -d
pl3as3_l3t_m3_1nt0_th3_saf3
```

```bash
➜  safeopener java SafeOpener
Enter password for the safe: pl3as3_l3t_m3_1nt0_th3_saf3
cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz
Sesame open
```

Flag : `picoCTF{pl3as3_l3t_m3_1nt0_th3_saf3}`

## unpackme.py

Can you get the flag?
Reverse engineer this Python program.

```python
import base64
from cryptography.fernet import Fernet

payload = b'gAAAAABiMD1Dt87s50caSunQlHoZqPOwtGNaQXexN-gjKwZeuLEN_-v6UcFJHVXOT4B6DcD1SB7cnd6yTcHg9e9LZCAeJY2cJ0r0sfyGPRiH60F-WbkyUjlAdDywI8RPdTpDYLuBmpZ_Kun-kHyTzMjeKR6R26Z4JITUS8n7Dj9X--9eNLajH6UuYD4GkjRACpsidl_8z33DlB86u_xDCMMm7HFK2oJTs8HG1fBex6enQsu0frUAJbx56DxhRvWawAysDMtLE50vaohrzkVV7Yaz4ClilwgfjQ=='

key_str = 'correctstaplecorrectstaplecorrec'
key_base64 = base64.b64encode(key_str.encode())
f = Fernet(key_base64)
plain = f.decrypt(payload)
exec(plain.decode())
```

We can simply run the program line by line via python interpreter to reverse engineer the code.

```python
>>> payload = b'gAAAAABiMD1Dt87s50caSunQlHoZqPOwtGNaQXexN-gjKwZeuLEN_-v6UcFJHVXOT4B6DcD1SB7cnd6yTcHg9e9LZCAeJY2cJ0r0sfyGPRiH60F-WbkyUjlAdDywI8RPdTpDYLuBmpZ_Kun-kHyTzMjeKR6R26Z4JITUS8n7Dj9X--9eNLajH6UuYD4GkjRACpsidl_8z33DlB86u_xDCMMm7HFK2oJTs8HG1fBex6enQsu0frUAJbx56DxhRvWawAysDMtLE50vaohrzkVV7Yaz4ClilwgfjQ=='
>>> key_str = 'correctstaplecorrectstaplecorrec'
>>> key_base64 = base64.b64encode(key_str.encode())
>>> key_base64
b'Y29ycmVjdHN0YXBsZWNvcnJlY3RzdGFwbGVjb3JyZWM='
>>> f = Fernet(key_base64)
>>> f
<cryptography.fernet.Fernet object at 0x7fc9bb354dc0>
>>> plain = f.decrypt(payload)
>>> plain
b"\npw = input('What\\'s the password? ')\n\nif pw == 'batteryhorse':\n  print('picoCTF{175_chr157m45_8aef58d2}')\nelse:\n  print('That password is incorrect.')\n\n"
```

```bash
➜  unpackmepy python unpackme.flag.py 
What's the password? batteryhorse
picoCTF{175_chr157m45_8aef58d2}
```

Flag : `picoCTF{175_chr157m45_8aef58d2}`

## bloat.py

Can you get the flag?
Run this Python program in the same directory as this encrypted flag.

```bash
➜  bloatpy python bloat.flag.py 
Please enter correct password for flag: dunno
That password is incorrect
```

Reviewing the python program, the function `arg133()` seems to be the place where the program is checking for user input for correct password.

```python
if arg432 == a[71]+a[64]+a[79]+a[79]+a[88]+a[66]+a[71]+a[64]+a[77]+a[66]+a[68]
```

```python
import sys
a = "!\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ"+ \
            "[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~ "

def arg133(arg432):
  if arg432 == a[71]+a[64]+a[79]+a[79]+a[88]+a[66]+a[71]+a[64]+a[77]+a[66]+a[68]:
    return True
  else:
    print(a[51]+a[71]+a[64]+a[83]+a[94]+a[79]+a[64]+a[82]+a[82]+a[86]+a[78]+\
a[81]+a[67]+a[94]+a[72]+a[82]+a[94]+a[72]+a[77]+a[66]+a[78]+a[81]+\
a[81]+a[68]+a[66]+a[83])
    sys.exit(0)
    return False

def arg111(arg444):
  return arg122(arg444.decode(), a[81]+a[64]+a[79]+a[82]+a[66]+a[64]+a[75]+\
a[75]+a[72]+a[78]+a[77])

def arg232():
  return input(a[47]+a[75]+a[68]+a[64]+a[82]+a[68]+a[94]+a[68]+a[77]+a[83]+\
a[68]+a[81]+a[94]+a[66]+a[78]+a[81]+a[81]+a[68]+a[66]+a[83]+\
a[94]+a[79]+a[64]+a[82]+a[82]+a[86]+a[78]+a[81]+a[67]+a[94]+\
a[69]+a[78]+a[81]+a[94]+a[69]+a[75]+a[64]+a[70]+a[25]+a[94])

def arg132():
  return open('flag.txt.enc', 'rb').read()

def arg112():
  print(a[54]+a[68]+a[75]+a[66]+a[78]+a[76]+a[68]+a[94]+a[65]+a[64]+a[66]+\
a[74]+a[13]+a[13]+a[13]+a[94]+a[88]+a[78]+a[84]+a[81]+a[94]+a[69]+\
a[75]+a[64]+a[70]+a[11]+a[94]+a[84]+a[82]+a[68]+a[81]+a[25])

def arg122(arg432, arg423):
    arg433 = arg423
    i = 0
    while len(arg433) < len(arg432):
        arg433 = arg433 + arg423[i]
        i = (i + 1) % len(arg423)        
    return "".join([chr(ord(arg422) ^ ord(arg442)) for (arg422,arg442) in zip(arg432,arg433)])
    
arg444 = arg132()
arg432 = arg232()
arg133(arg432)
arg112()
arg423 = arg111(arg444)
print(arg423)
sys.exit(0)
```

So we can simply reverse engineer the password from the python interpreter as below.

```python
>>> a = "!\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ"+ \
            "[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~ "
>>> a[71]+a[64]+a[79]+a[79]+a[88]+a[66]+a[71]+a[64]+a[77]+a[66]+a[68]
'happychance'
```

```bash
➜  bloatpy python bloat.flag.py 
Please enter correct password for flag: happychance
Welcome back... your flag, user:
picoCTF{d30bfu5c4710n_f7w_c47f9e9c}
```

Flag : `picoCTF{d30bfu5c4710n_f7w_c47f9e9c}`

## Fresh java

Can you get the flag?
Reverse engineer this Java program.

The java program is loaded into ghidra.

![ghidra](https://bn1304files.storage.live.com/y4mTarSwIjZaWdy2yGbyHnir3TbP0GL0fPaHD1enBKLCIGTLpvaDCzH3WZQPE6wZeqYUK23825rsncI8lugUbvZ7FM7FhjGIUXmMy0HW5MxLPrIAHLR89q6FUJkodF0V6tvQZbwTV7Hw1TnBL5gDDwPG1XBu6KPzsSiWtMR-DMAnI9kcxHte0DvFOmehX-LX3I7?width=2860&height=1724&cropmode=none)

From the program's logic, we can see that it is checking each character for correct password. From here we can manually reverse engineer the password.

![repasswd](https://bn1304files.storage.live.com/y4m6kgyqvVVFRCCCj7hSSKhvoqlujSzdCac6asg7C8Tb_cSlJ3sGy9sBQc-GNzB0SV55kqjNOPzRGrvi2I1xSURcFOJ2sZlE5bIb3xyKhO9AuE6HIzwFDh0F7xoeXXxV8n206Dj4d2S_i_8mBfg-4esLCTMDgYsu6GId-EySQtIqzAlXm2bALTQPQAQzniJ9uBw?width=2860&height=1732&cropmode=none)

```java
  cVar3 = objectRef.charAt(0x21);
  if (cVar3 != '}') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x20);
  if (cVar3 != '0') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1f);
  if (cVar3 != 'f') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1e);
  if (cVar3 != '9') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1d);
  if (cVar3 != '5') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1c);
  if (cVar3 != 'c') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1b);
  if (cVar3 != '6') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x1a);
  if (cVar3 != '2') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x19);
  if (cVar3 != '1') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x18);
  if (cVar3 != '_') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x17);
  if (cVar3 != 'd') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x16);
  if (cVar3 != '3') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x15);
  if (cVar3 != 'r') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x14);
  if (cVar3 != '1') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x13);
  if (cVar3 != 'u') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x12);
  if (cVar3 != 'q') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x11);
  if (cVar3 != '3') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0x10);
  if (cVar3 != 'r') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0xf);
  if (cVar3 != '_') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0xe);
  if (cVar3 != 'g') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0xd);
  if (cVar3 != 'n') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0xc);
  if (cVar3 != '1') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0xb);
  if (cVar3 != 'l') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(10);
  if (cVar3 != '0') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(9);
  if (cVar3 != '0') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(8);
  if (cVar3 != '7') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(7);
  if (cVar3 != '{') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(6);
  if (cVar3 != 'F') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(5);
  if (cVar3 != 'T') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(4);
  if (cVar3 != 'C') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(3);
  if (cVar3 != 'o') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(2);
  if (cVar3 != 'c') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(1);
  if (cVar3 != 'i') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
  cVar3 = objectRef.charAt(0);
  if (cVar3 != 'p') {
    pPVar1 = System.out;
    pPVar1.println("Invalid key");
    return;
  }
```

```bash
➜  freshjava java KeygenMe
Enter key:
picoCTF{700l1ng_r3qu1r3d_126c59f0}
Valid key
```

Flag : `picoCTF{700l1ng_r3qu1r3d_126c59f0}`

## Bbbbloat

Can you get the flag?
Reverse engineer this binary.

```bash
➜  Bbbbloat ./bbbbloat 
What's my favorite number? 99
Sorry, that's not it!
```

Using r2 we can dynamically debug the program as below.

```bash
➜  Bbbbloat r2 -d bbbbloat 
Process with PID 68182 started...
= attach 68182 68182
bin.baddr 0x559726213000
Using 0x559726213000
asm.bits 64
Warning: r_bin_file_hash: file exceeds bin.hashlimit
 -- Mind the trap.
[0x7fac5c90e100]> aaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Check for objc references
[x] Check for vtables
[TOFIX: aaft can't run in debugger mode.ions (aaft)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information
[x] Use -AA or aaaa to perform additional experimental analysis.
[0x7fac5c90e100]> afll
address            size  nbbs edges    cc cost          min bound range max bound          calls locals args xref frame name
================== ==== ===== ===== ===== ==== ================== ===== ================== ===== ====== ==== ==== ===== ====
0x0000559726214160   46     1     0     1   15 0x0000559726214160    46 0x000055972621418e     1    0      1    0     8 entry0
0x0000559726216fe0 4121     1     0     1 2060 0x0000559726216fe0  4121 0x0000559726217ff9     0    0      0    1     0 reloc.__libc_start_main
0x00005597262140d0   11     1     0     1    4 0x00005597262140d0    11 0x00005597262140db     0    0      0    1     0 sym.imp.free
0x00005597262140e0   11     1     0     1    4 0x00005597262140e0    11 0x00005597262140eb     0    0      0    1     0 sym.imp.putchar
0x00005597262140f0   11     1     0     1    4 0x00005597262140f0    11 0x00005597262140fb     0    0      0    1     0 sym.imp.puts
0x0000559726214100   11     1     0     1    4 0x0000559726214100    11 0x000055972621410b     0    0      0    0     0 sym.imp.strlen
0x0000559726214110   11     1     0     1    4 0x0000559726214110    11 0x000055972621411b     0    0      0    1     0 sym.imp.__stack_chk_fail
0x0000559726214120   11     1     0     1    4 0x0000559726214120    11 0x000055972621412b     0    0      0    1     0 sym.imp.printf
0x0000559726214130   11     1     0     1    4 0x0000559726214130    11 0x000055972621413b     0    0      0    1     0 sym.imp.fputs
0x0000559726213000  341     3     2     3  158 0x0000559726213000   348 0x000055972621315c     0    0      2    0     0 loc.imp._ITM_deregisterTMCloneTable
0x0000559726214140   11     1     0     1    4 0x0000559726214140    11 0x000055972621414b     0    0      0    1     0 sym.imp.__isoc99_scanf
0x0000559726214150   11     1     0     1    4 0x0000559726214150    11 0x000055972621415b     0    0      0    0     0 sym.imp.strdup
0x0000559726214240   60     5     5     4   22 0x00005597262141c0   137 0x0000559726214249     0    0      0    0     0 entry.init0
0x0000559726214200   54     5     5     4   24 0x0000559726214200    57 0x0000559726214239     2    0      0    0     8 entry.fini0
0x00005597262140c0   11     1     0     1    4 0x00005597262140c0    11 0x00005597262140cb     0    0      0    1     0 fcn.5597262140c0
0x0000559726214190   34     4     4     4   14 0x0000559726214190    41 0x00005597262141b9     0    0      0    1     0 fcn.559726214190
0x0000559726214307  675     6     6     4  178 0x0000559726214307   675 0x00005597262145aa     8   10      2    1    88 main
[0x7fac5c90e100]> db main
[0x7fac5c90e100]> pdf@main
            ; DATA XREF from entry0 @ 0x559726214181
┌ 675: int main (int argc, char **argv, char **envp);
│           ; var int64_t var_50h @ rbp-0x50
│           ; var int64_t var_44h @ rbp-0x44
│           ; var int64_t var_40h @ rbp-0x40
│           ; var int64_t var_3ch @ rbp-0x3c
│           ; var int64_t var_38h @ rbp-0x38
│           ; var int64_t var_30h @ rbp-0x30
│           ; var int64_t var_28h @ rbp-0x28
│           ; var int64_t var_20h @ rbp-0x20
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int argc @ rdi
│           ; arg char **argv @ rsi
│           0x559726214307 b    f30f1efa       endbr64
│           0x55972621430b      55             push rbp
│           0x55972621430c      4889e5         mov rbp, rsp
│           0x55972621430f      4883ec50       sub rsp, 0x50
│           0x559726214313      897dbc         mov dword [var_44h], edi ; argc
│           0x559726214316      488975b0       mov qword [var_50h], rsi ; argv
[0x7fac5c90e100]> db 0x5597262144cb
[0x7fac5c90e100]> dc
hit breakpoint at: 559726214307
[0x55972621430c]> dc
What's my favorite number? 99
hit breakpoint at: 5597262144cb
[0x5597262144cb]> dr
rax = 0x00000063
rbx = 0x5597262145b0
rcx = 0x00000000
rdx = 0x000d2c49
r8 = 0x0000000a
r9 = 0x00000000
r10 = 0x7fac5c892ac0
r11 = 0x00000000
r12 = 0x559726214160
r13 = 0x7ffec33e00b0
r14 = 0x00000000
r15 = 0x00000000
rsi = 0x000d2c49
rdi = 0x000d2c49
rsp = 0x7ffec33dff70
rbp = 0x7ffec33dffc0
rip = 0x5597262144cb
rflags = 0x00000202
orax = 0xffffffffffffffff
[0x5597262144cb]> dr rax=0x86187
0x00000063 ->0x00086187
[0x5597262144cb]> dc
picoCTF{cu7_7h3_bl047_2d7aeca1}
[0x7fac5c7da176]> 
[0x7fac5c7da176]> dc
child exited with status 0

==> Process finished
```

Flag : `picoCTF{cu7_7h3_bl047_2d7aeca1}`

## unpackme

Can you get the flag?
Reverse engineer this binary.

```bash
➜  unpackeme file unpackme-upx 
unpackme-upx: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, no section header
➜  unpackeme upx -d unpackme-upx -o unpackme
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
   1002408 <-    379116   37.82%   linux/amd64   unpackme

Unpacked 1 file.
➜  unpackeme ./unpackme 
What's my favorite number? 223
Sorry, that's not it!
```

Alright time to reverse engineer the binary after unpacking.

![1](https://bn1304files.storage.live.com/y4mV4EySxpD-ds-sjaeXk5i7UL8kUKLKU5kj-KHs4IYUJiE1LVyZ2y4eVEXSbqqv3MWKaYMl39K25djJPfnWHiiQz4J-EIx6TFC7eHIyOQV_SW71CRO7UaF1WSf9TRPQoNoZ5sjDY5bUMZasXy6uasqe85qFOtrIJzxM8BMYXSidbd9yuf_Tz1ZRQhO5fK8Rifl?width=2860&height=1732&cropmode=none)

![2](https://bn1304files.storage.live.com/y4mYJs-y-nePNiqnLFlhAMQblEs-bf8Mehff9d2zpr3Yh_gXkLBvAl6VA6keEq7J8GmQGD02noZvJO1NSrC_77-tq6lvLxR9EtJYYPkZkEcF03iy7rE-aKN5Bk1T56K-keshlUQPnUrMbJG_5s0DvttgZcc5yTeZFUXSMD4VgZVEf6Wukqi05n2UCOjwW-xTKFD?width=2860&height=1732&cropmode=none)

![3](https://bn1304files.storage.live.com/y4m1-HwKOFSnekV_Wl6inu0UFBiHgzQbLFFlwnl5bAw9Cf7CdRvyPkiExaxjdRZhmX31ycGo883WClg46hcot-TExh-IoMeNoCBzkD5rhEyqQII6cuoduwFsI7KNT6OJMmu7Mcq5sN8C4WnV5UJDoPQTr4uf2zXI7bjfj7zqtYft6E1dXxs5rTx_xY9Tf2Zn0ku?width=2860&height=1732&cropmode=none)

![4](https://bn1304files.storage.live.com/y4mlFbsKUi5n1UP_6GC2lODp7EIgQBzBwk0XVE00BG9fMG5a19W7figOyg_IuB15uow4Su_mF0F_szEBnBI9w93anhLNmyla0afKfYsxinkh9KgxOLtSWBkCBJ2aPJnf_yX_MipfVu8bJPvNbM706Z9LCvcm3B0qUAk-o_8hgFHQCkwe_3GxsTdVFkPIZWhZW9I?width=2860&height=1732&cropmode=none)

![5](https://bn1304files.storage.live.com/y4mw_7Nt7F_A2fnhkpncLXdA_9flXeC98_zBYCj0-OxIQlniu4tRUmNwMF3k-HAZlq0j7xQAHaZQZYtTHkDLOgNmRO_KgipawT_4OLW6WG6G4LjBE9U-WWYCc_gwmLmBUOloBeG4JeDFWN-XGpSj7evMQ7xb276s0bETRezArkj0-AMrc2jZkG9KtIHppvOYhMi?width=2860&height=1732&cropmode=none)

```bash
➜  unpackeme gdb unpackme
(gdb) set disassembly-flavor intel
(gdb) disassemble main
(gdb) break main
Breakpoint 1 at 0x401e73
(gdb) break *(main+133)
Breakpoint 2 at 0x401ef8
(gdb) run
Starting program: /cases/pico2022/revEngineer/unpackeme/unpackme

Breakpoint 1, 0x0000000000401e73 in main ()
(gdb) continue
Continuing.
What's my favorite number? 99
Breakpoint 2, 0x0000000000401ef8 in main ()
rax            0x63                99
rbx            0x400530            4195632
rcx            0x0                 0
rdx            0x0                 0
rsi            0x0                 0
rdi            0x7fffffffd930      140737488345392
rbp            0x7fffffffdec0      0x7fffffffdec0
rsp            0x7fffffffde70      0x7fffffffde70
r8             0xa                 10
r9             0x0                 0
r10            0x4ba400            4957184
r11            0x0                 0
r12            0x402ff0            4206576
r13            0x0                 0
--Type <RET> for more, q to quit, c to continue without paging--
(gdb) set $eax=0xb83cb 
rax            0xb83cb             754635
rbx            0x400530            4195632
rcx            0x0                 0
rdx            0x0                 0
rsi            0x0                 0
rdi            0x7fffffffd930      140737488345392
rbp            0x7fffffffdec0      0x7fffffffdec0
rsp            0x7fffffffde70      0x7fffffffde70
r8             0xa                 10
r9             0x0                 0
r10            0x4ba400            4957184
r11            0x0                 0
r12            0x402ff0            4206576
r13            0x0                 0
--Type <RET> for more, q to quit, c to continue without paging--
(gdb) ni
0x0000000000401efd in main ()
0x0000000000401eff in main ()
(gdb) continue
Continuing.
picoCTF{up><_m3_f7w_ed7b0850}
[Inferior 1 (process 67587) exited normally]
```

Flag : `picoCTF{up><_m3_f7w_ed7b0850}`
