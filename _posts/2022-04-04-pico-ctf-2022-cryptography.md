---
layout: post
title: 'Pico CTF 2022: Cryptography 🔒 🕵'
date: 2022-04-04 11:35 +0800
render_with_liquid: false
categories:
- CTF
- Cryptography
tags:
- CTF
- Cryptography
- picoctf-2022
---

## basic-mod1

We found this weird message being passed around on the servers, we think we have a working decrpytion scheme.
Download the message here.
Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.
Wrap your decrypted message in the picoCTF flag format (i.e. picoCTF{decrypted_message})

```python
import string

deciDigits = ['26', '27', '28', '29', '30', '31', '32', '33', '34', '35']
numDict = {}
for idx in range(len(deciDigits)):
    numDict[idx] = deciDigits[idx]
numDict = {v: k for k, v in numDict.items()}

alphaDict = dict(enumerate(string.ascii_uppercase))

def readFileNDecode():
    with open('message.txt', 'r') as msg:
        flag = []
        msg_list = list(filter(None, msg.read().split(' ')))
        for i in msg_list:
            i = int(i)
            modNum = i % 37
            if modNum < 26:
                flag.append(alphaDict[modNum])
            elif modNum < 36:
                flag.append(numDict[str(modNum)])
            elif modNum == 36:
                flag.append('_')
        flag = map(str, flag)
        flag = ''.join(flag)
        picoFlag = f'picoCTF{{{flag}}}'
        print(picoFlag)
        
    
readFileNDecode()
```

```bash
➜  basicmod1 python basicmod1.py 
picoCTF{R0UND_N_R0UND_8C863EE7}
```

Flag : `picoCTF{R0UND_N_R0UND_8C863EE7}`

## substitution0

A message has come in but it seems to be all scrambled. Luckily it seems to have the key at the beginning. Can you crack this substitution cipher?

Cipher text as below. The key is the `PAQZTNDSRYWFEXGJKCVLBUHOMI`

```text
PAQZTNDSRYWFEXGJKCVLBUHOMI 

Stctbjgx Ftdcpxz pcgvt, hrls p dcput pxz vlpltfm prc, pxz acgbdsl et lst attlft
ncge p dfpvv qpvt rx hsrqs rl hpv txqfgvtz. Rl hpv p atpblrnbf vqpcpaptbv, pxz, pl
lspl lret, bxwxghx lg xplbcpfrvlv—gn qgbcvt p dctpl jcrit rx p vqrtxlrnrq jgrxl
gn urth. Lstct htct lhg cgbxz afpqw vjglv xtpc gxt tolcterlm gn lst apqw, pxz p
fgxd gxt xtpc lst glstc. Lst vqpftv htct toqttzrxdfm spcz pxz dfgvvm, hrls pff lst
pjjtpcpxqt gn abcxrvstz dgfz. Lst htrdsl gn lst rxvtql hpv utcm ctepcwpaft, pxz,
lpwrxd pff lsrxdv rxlg qgxvrztcplrgx, R qgbfz spczfm afpet Ybjrltc ngc srv gjrxrgx
ctvjtqlrxd rl.

Lst nfpd rv: jrqgQLN{5BA5717B710X_3U0FB710X_PP1QQ2A7}
```

Using <https://www.dcode.fr/substitution-monoalphabetique>

```text
HEREUPON LEGRAND AROSE, WITH A GRAVE AND STATELY AIR, AND BROUGHT ME THE BEETLE
FROM A GLASS CASE IN WHICH IT WAS ENCLOSED. IT WAS A BEAUTIFUL SCARABAEUS, AND, AT
THAT TIME, UNKNOWN TO NATURALISTS--OF COURSE A GREAT PRIZE IN A SCIENTIFIC POINT
OF VIEW. THERE WERE TWO ROUND BLACK SPOTS NEAR ONE EXTREMITY OF THE BACK, AND A
LONG ONE NEAR THE OTHER. THE SCALES WERE EXCEEDINGLY HARD AND GLOSSY, WITH ALL THE
APPEARANCE OF BURNISHED GOLD. THE WEIGHT OF THE INSECT WAS VERY REMARKABLE, AND,
TAKING ALL THINGS INTO CONSIDERATION, I COULD HARDLY BLAME JUPITER FOR HIS OPINION
RESPECTING IT.

THE FLAG IS: PICOCTF{5UB5717U710N_3V0LU710N_AA1CC2B7}
```

Flag : `PICOCTF{5UB5717U710N_3V0LU710N_AA1CC2B7}`

## basic-mod2

Description

A new modular challenge!
Download the message here.
Take each number mod 41 and find the modular inverse for the result. Then map to the following character set: 1-26 are the alphabet, 27-36 are the decimal digits, and 37 is an underscore.
Wrap your decrypted message in the picoCTF flag format (i.e. picoCTF{decrypted_message})

```python
import string

deciDigits = ['27', '28', '29', '30', '31', '32', '33', '34', '35', '36']
numDict = {}
for idx in range(len(deciDigits)):
    numDict[idx] = deciDigits[idx]
numDict = {v: k for k, v in numDict.items()}

alphaDict = dict(enumerate(string.ascii_uppercase, 1))


def egcd(a, b):
    '''helper function to find modinv'''
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)


def modinv(a, m):
    '''helper function to find modinv'''
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m


def readFileNDecode():
    with open('message.txt', 'r') as msg:
        flag = []
        msg_list = list(filter(None, msg.read().split(' ')))
        for i in msg_list:
            i = int(i)
            modInvNum = modinv(i, 41)
            if modInvNum < 27:
                flag.append(alphaDict[modInvNum])
            elif modInvNum < 37:
                flag.append(numDict[str(modInvNum)])
            elif modInvNum == 37:
                flag.append('_')
        flag = map(str, flag)
        flag = ''.join(flag)
        picoFlag = f"picoCTF{{{flag}}}"
        print(picoFlag)


readFileNDecode()
```

```bash
➜  pico2022 git:(main) ✗ python basicmod2.py 
picoCTF{1NV3R53LY_H4RD_F6747912}
```

Flag : `picoCTF{1NV3R53LY_H4RD_F6747912}`

## Vignere

Can you decrypt this message?
Decrypt this message using this key "CYLAB". `rgnoDVD{O0NU_WQ3_G1G3O3T3_A1AH3S_e481bf5f}`

Flag : `picoCTF{D0NT_US3_V1G3N3R3_C1PH3R_c481du5f}`
Using <https://www.dcode.fr/vigenere-cipher>

## credstuff

We found a leak of a blackmarket website's login credentials. Can you find the password of the user cultiris and successfully decrypt it?
Download the leak here.
The first user in usernames.txt corresponds to the first password in passwords.txt. The second user corresponds to the second password, and so on.

Flag : `picoCTF{C7r1F_54V35_71M3}`

![image](https://bn1304files.storage.live.com/y4mV2YsQWbh2VIpNw1ad0aNSTaB9erEuzEMB9qn9DTePZHr4nV4lXLI5qndi1Tx9uN68wZtIGR8eMrOFKm557DhO1xLUbd-H7RWg2F4ChV4SQ3ofSwZJX0kExHhr-HdHcKKfgM6j-uhjSvAPASK1FznFL8Gu29MVGecj0z17Ek40INgP7XotnjmZzjb83Yglk_1?width=2654&height=1620&cropmode=none)

`cultiris:cvpbPGS{P7e1S_54I35_71Z3}`

Using [caesar-cipher](https://www.dcode.fr/caesar-cipher).

## rail-fence

A type of transposition cipher is the rail fence cipher, which is described here. Here is one such cipher encrypted using the rail fence with 4 rails. Can you decrypt it?
Download the message here.
Put the decoded message in the picoCTF flag format, picoCTF{decoded_message}.
`Ta _7N6D54hlg:W3D_H3C31N__510ef sHR053F38N43D28 i33___N2`

Flag : `picoCTF{WH3R3_D035_7H3_F3NC3_8361N_4ND_3ND_55228140}`

Using cyberchef : <https://gchq.github.io/CyberChef/#recipe=Rail_Fence_Cipher_Decode(4,0)&input=VGEgXzdONkQ1NGhsZzpXM0RfSDNDMzFOX181MTBlZiBzSFIwNTNGMzhONDNEMjggaTMzX19fTjI>

`The flag is: WH3R3_D035_7H3_F3NC3_8361N_4ND_3ND_55228140`

## substitution1

A second message has come in the mail, and it seems almost identical to the first one. Maybe the same thing will work again.
Download the message here.

`WILh (hjpai lpa wrtikan ijn lbrc) ran r ietn pl wpgtkina hnwkazie wpgtnizizpu. Wpuinhiruih ran tanhnuiny szij r hni pl wjrbbnucnh sjzwj inhi ijnza wanrizdzie, inwjuzwrb (ruy cppcbzuc) hfzbbh, ruy tapobng-hpbdzuc rozbzie. Wjrbbnucnh khkrbbe wpdna r ukgona pl wrincpaznh, ruy sjnu hpbdny, nrwj eznbyh r hiazuc (wrbbny r lbrc) sjzwj zh hkogziiny ip ru pubzun hwpazuc hnadzwn. WILh ran r canri sre ip bnrau r szyn raare pl wpgtkina hnwkazie hfzbbh zu r hrln, bncrb nudzapugnui, ruy ran jphiny ruy tbreny oe grue hnwkazie capkth rapkuy ijn spaby lpa lku ruy tarwizwn. Lpa ijzh tapobng, ijn lbrc zh: tzwpWIL{LA3VK3UWE_4774WF5_4A3_W001_O810YY84}`

Flag : `picoCTF{FR3QU3NCY_4774CK5_4R3_C001_B810DD84}`

Using <https://www.dcode.fr/monoalphabetic-substitution>

`CTFS (SHORT FOR CAPTURE THE FLAG) ARE A TYPE OF COMPUTER SECURITY COMPETITION. CONTESTANTS ARE PRESENTED WITH A SET OF CHALLENGES WHICH TEST THEIR CREATIVITY, TECHNICAL (AND GOOGLING) SKILLS, AND PROBLEM-SOLVING ABILITY. CHALLENGES USUALLY COVER A NUMBER OF CATEGORIES, AND WHEN SOLVED, EACH YIELDS A STRING (CALLED A FLAG) WHICH IS SUBMITTED TO AN ONLINE SCORING SERVICE. CTFS ARE A GREAT WAY TO LEARN A WIDE ARRAY OF COMPUTER SECURITY SKILLS IN A SAFE, LEGAL ENVIRONMENT, AND ARE HOSTED AND PLAYED BY MANY SECURITY GROUPS AROUND THE WORLD FOR FUN AND PRACTICE. FOR THIS PROBLEM, THE FLAG IS: PICOCTF{FR3QU3NCY_4774CK5_4R3_C001_B810DD84}`
