---
layout: post
title: "Sensitive data redaction/unredaction tool - PyRedactKit \U0001F9F0\U0001F510\U0001F4DD"
categories:
- Projects
- security-toolkit
tags:
- Projects
- security-toolkit
- Python
- Data-redaction
- data-security
- log-redaction
render_with_liquid: false
date: 2022-05-19 16:18 +0800
---
# Introduction

> Check out the [project here](<https://github.com/brootware/PyRedactKit>)!

> To start using: `pip install --upgrade pyredactkit && prk`

There are a lot of open-source cyber security tools that help automate a lot of the tasks on the Red team/PT side of things like domain enumeration, network scanning, etc. When I searched for a Blue team/defense-focused tool for security and privacy especially redacting data, I could only find a few of them on Github. The PyRedactKit is an inspiration and expansion of Ben's original [redact-py CLI tool](https://github.com/ben-labs/redact-py) that redacts IP addresses from log files.

PyRedactKit aims to sanitize and redact Personally Identifiable Information (PII) such as Names, Email addresses, Credit Card, SG NRIC and other sensitive data like internal IP addresses and domain names. One great use case of the tool would be if you are working in a SOC and you have to send some log data to a 3rd party for troubleshooting or whatever reason, PyRedactKit can come in handy to redact those sneaky dns and ip addresses.

# First implementation

First implementation of the PyRedactKit was straightforward. I took inspiration and used libraries from a couple of open source projects  [commonregex](https://github.com/madisonmay/CommonRegex), an open source project by Madison May on github. There are simple wrapper functions that calls the commonregex library to search for the sensitive data in a text file. It worked great for a start.

But as I ran it on bigger and bigger log files with a few hundred thousands of records, the run time suffered.

# Bottle necks and slow performance

## Single-file processing bottle neck

```bash
➜  PyRedactKit git:(600fc8f) ✗ time python pyredactkit.py ip_test.txt                                                               
 
    ______       ______         _            _     _   ___ _   
    | ___ \      | ___ \       | |          | |   | | / (_) |  
    | |_/ /   _  | |_/ /___  __| | __ _  ___| |_  | |/ / _| |_ 
    |  __/ | | | |    // _ \/ _` |/ _` |/ __| __| |    \| | __|
    | |  | |_| | | |\ \  __/ (_| | (_| | (__| |_  | |\  \ | |_ 
    \_|   \__, | \_| \_\___|\__,_|\__,_|\___|\__| \_| \_/_|\__|
           __/ |                                               
           |___/                                                                                                           
            +-+-+-+-+-+-+-+ +-+-+ +-+-+-+-+-+-+-+-+-+
            |P|o|w|e|r|e|d| |b|y| |B|r|o|o|t|w|a|r|e|
            +-+-+-+-+-+-+-+ +-+-+ +-+-+-+-+-+-+-+-+-+                                                                             
     
[ + ] Processing starts now. This may take some time depending on the file size. Monitor the redacted file size to monitor progress
[ + ] No option supplied, will be redacting all the sensitive data supported

[ + ] Redacted 10021 targets...
[ + ] Took 38.12691903114319 seconds to execute
python3 pyredactkit.py ip_test.txt  67.39s user 0.19s system 99% cpu 1:08.05 total
```

For a single file with over 10k lines of records, it took over a minute to complete. If we pay attention to the last two lines, there were 2 parts that were taking half a minute each.

```console
[ + ] Took 38.12691903114319 seconds to execute
python3 pyredactkit.py ip_test.txt  67.39s user 0.19s system 99% cpu 1:08.05 total
```

The first part of the bottleneck is coming from the commonregex library that is returning the list of identified words to be redacted from the text file. Especially the `re.compile` of regex library which is doing the compilation and searching that takes more time during the redaction.

The second part was on this [particular function](https://github.com/brootware/PyRedactKit/blob/600fc8f65d629560608fc436f61614bc74a427e8/src/redact.py#L174).

```python
    def to_redact(self, data=str, redact_list=[]):
        redact_count = 0
        start = time.time()

        for elm in redact_list:
            total_elm = len(elm)
            # encode element to be blocked
            elm = r'\b' + elm + r'\b'
            # multiply the block with length of identified elements
            bl = total_elm * self.block
            # substitute the block using regular expression
            data = re.sub(elm, bl, data)
            redact_count += 1

        end = time.time()
        print()
        print(f"[ + ] Redacted {redact_count} targets...")
        time_taken = end - start
        print(f'[ + ] Took {time_taken} seconds to execute')
        return data
```

The function was simply iterating through a list of identified strings returned from the commonregex regular expression library and redacting them from the text files. In terms of time complexity this was linear time increase directly proportional to number of elements in the list. O(n) The function itself is not really that big of an issue.

If we were to run this for multiple log files with hundred thousands of records, the redaction will take much much longer.

## Refactoring the core redaction engine

The very initial implementation and the architecture of the redaction engine looks something like this.

Class diagram:
![class](https://github.com/brootware/PyRedactKit/blob/main/images/beforeRefactor/classes.png?raw=true)

Program flow diagram:
![program](<https://github.com/brootware/PyRedactKit/blob/main/images/beforeRefactor/packages.png?raw=true>)

As you can see the program suffers from a code smell. The redactor class was actually suffering from [Large Class symptom](https://refactoring.guru/smells/large-class). It is simply when a class contains many fields/methods/lines of code.

So to remedy this, I broke down the Redactor class into 3 separate components:

* Core Redactor Engine - contains a pre-defined list of regex patterns to mask sensitive data.
* Custom Redactor Engine - reads in user defined list of regex patterns to mask sensitive data.
* Common Jobs - common functions that is used by both core and custom redactor engines.

Class diagram:
![class](<https://github.com/brootware/PyRedactKit/blob/main/images/refactor10jul/classes_pyredactkit.png?raw=true>)

Program flow diagram:
![program](<https://github.com/brootware/PyRedactKit/blob/main/images/refactor10jul/packages_pyredactkit.png?raw=true>)

This made the code more modular and easier to focus and contributors can work on different parts of the code base simultaneously.

## Optimizing core redaction engine for speed

A solution was considered to refactor the function to use binary search instead of iterative linear search. However, as the list could not really be sorted I had to come up with alternative solution.

An alternative to this is to not store the identified regex strings in a list but rather redact them on the fly. In order to achieve that I had to stop using the commonregex library by madison may and build the regex library from scratch.

So a separate class for identifier is created to maintain a database of regular expressions with data type for redaction as shown in the class diagram in refactoring section of this article. This made the project easily extensible. Contributors can also extend the type of data they want to redact just by modifying this particular [regex library](https://github.com/brootware/PyRedactKit/blob/233452755f5ee85a29f96f85d6b0a85052425b54/pyredactkit/identifiers.py#L17).

```json
[
    {
        "pattern": r"([a-z0-9!#$%&'*+\/=?^_`{|.}~-]+@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?)",
        "type": ("emails", "email")
    },
    {
        "pattern": r'(?i)((?:https?://|www\d{0,3}[.])?[a-z0-9.\-]+[.](?:(?:international)|(?:construction)|(?:contractors)|(?:enterprises)|(?:photography)|(?:immobilien)|(?:management)|(?:technology)|(?:directory)|(?:education)|(?:equipment)|(?:institute)|(?:marketing)|(?:solutions)|(?:builders)|(?:clothing)|(?:computer)|(?:democrat)|(?:diamonds)|(?:graphics)|(?:holdings)|(?:lighting)|(?:plumbing)|(?:training)|(?:ventures)|(?:academy)|(?:careers)|(?:company)|(?:domains)|(?:florist)|(?:gallery)|(?:guitars)|(?:holiday)|(?:kitchen)|(?:recipes)|(?:shiksha)|(?:singles)|(?:support)|(?:systems)|(?:agency)|(?:berlin)|(?:camera)|(?:center)|(?:coffee)|(?:estate)|(?:kaufen)|(?:luxury)|(?:monash)|(?:museum)|(?:photos)|(?:repair)|(?:social)|(?:tattoo)|(?:travel)|(?:viajes)|(?:voyage)|(?:build)|(?:cheap)|(?:codes)|(?:dance)|(?:email)|(?:glass)|(?:house)|(?:ninja)|(?:photo)|(?:shoes)|(?:solar)|(?:today)|(?:aero)|(?:arpa)|(?:asia)|(?:bike)|(?:buzz)|(?:camp)|(?:club)|(?:coop)|(?:farm)|(?:gift)|(?:guru)|(?:info)|(?:jobs)|(?:kiwi)|(?:land)|(?:limo)|(?:link)|(?:menu)|(?:mobi)|(?:moda)|(?:name)|(?:pics)|(?:pink)|(?:post)|(?:rich)|(?:ruhr)|(?:sexy)|(?:tips)|(?:wang)|(?:wien)|(?:zone)|(?:biz)|(?:cab)|(?:cat)|(?:ceo)|(?:com)|(?:edu)|(?:gov)|(?:int)|(?:mil)|(?:net)|(?:onl)|(?:org)|(?:pro)|(?:red)|(?:tel)|(?:uno)|(?:xxx)|(?:ac)|(?:ad)|(?:ae)|(?:af)|(?:ag)|(?:ai)|(?:al)|(?:am)|(?:an)|(?:ao)|(?:aq)|(?:ar)|(?:as)|(?:at)|(?:au)|(?:aw)|(?:ax)|(?:az)|(?:ba)|(?:bb)|(?:bd)|(?:be)|(?:bf)|(?:bg)|(?:bh)|(?:bi)|(?:bj)|(?:bm)|(?:bn)|(?:bo)|(?:br)|(?:bs)|(?:bt)|(?:bv)|(?:bw)|(?:by)|(?:bz)|(?:ca)|(?:cc)|(?:cd)|(?:cf)|(?:cg)|(?:ch)|(?:ci)|(?:ck)|(?:cl)|(?:cm)|(?:cn)|(?:co)|(?:cr)|(?:cu)|(?:cv)|(?:cw)|(?:cx)|(?:cy)|(?:cz)|(?:de)|(?:dj)|(?:dk)|(?:dm)|(?:do)|(?:dz)|(?:ec)|(?:ee)|(?:eg)|(?:er)|(?:es)|(?:et)|(?:eu)|(?:fi)|(?:fj)|(?:fk)|(?:fm)|(?:fo)|(?:fr)|(?:ga)|(?:gb)|(?:gd)|(?:ge)|(?:gf)|(?:gg)|(?:gh)|(?:gi)|(?:gl)|(?:gm)|(?:gn)|(?:gp)|(?:gq)|(?:gr)|(?:gs)|(?:gt)|(?:gu)|(?:gw)|(?:gy)|(?:hk)|(?:hm)|(?:hn)|(?:hr)|(?:ht)|(?:hu)|(?:id)|(?:ie)|(?:il)|(?:im)|(?:in)|(?:io)|(?:iq)|(?:ir)|(?:is)|(?:it)|(?:je)|(?:jm)|(?:jo)|(?:jp)|(?:ke)|(?:kg)|(?:kh)|(?:ki)|(?:km)|(?:kn)|(?:kp)|(?:kr)|(?:kw)|(?:ky)|(?:kz)|(?:la)|(?:lb)|(?:lc)|(?:li)|(?:lk)|(?:lr)|(?:ls)|(?:lt)|(?:lu)|(?:lv)|(?:ly)|(?:ma)|(?:mc)|(?:md)|(?:me)|(?:mg)|(?:mh)|(?:mk)|(?:ml)|(?:mm)|(?:mn)|(?:mo)|(?:mp)|(?:mq)|(?:mr)|(?:ms)|(?:mt)|(?:mu)|(?:mv)|(?:mw)|(?:mx)|(?:my)|(?:mz)|(?:na)|(?:nc)|(?:ne)|(?:nf)|(?:ng)|(?:ni)|(?:nl)|(?:no)|(?:np)|(?:nr)|(?:nu)|(?:nz)|(?:om)|(?:pa)|(?:pe)|(?:pf)|(?:pg)|(?:ph)|(?:pk)|(?:pl)|(?:pm)|(?:pn)|(?:pr)|(?:ps)|(?:pt)|(?:pw)|(?:py)|(?:qa)|(?:re)|(?:ro)|(?:rs)|(?:ru)|(?:rw)|(?:sa)|(?:sb)|(?:sc)|(?:sd)|(?:se)|(?:sg)|(?:sh)|(?:si)|(?:sj)|(?:sk)|(?:sl)|(?:sm)|(?:sn)|(?:so)|(?:sr)|(?:st)|(?:su)|(?:sv)|(?:sx)|(?:sy)|(?:sz)|(?:tc)|(?:td)|(?:tf)|(?:tg)|(?:th)|(?:tj)|(?:tk)|(?:tl)|(?:tm)|(?:tn)|(?:to)|(?:tp)|(?:tr)|(?:tt)|(?:tv)|(?:tw)|(?:tz)|(?:ua)|(?:ug)|(?:uk)|(?:us)|(?:uy)|(?:uz)|(?:va)|(?:vc)|(?:ve)|(?:vg)|(?:vi)|(?:vn)|(?:vu)|(?:wf)|(?:ws)|(?:ye)|(?:yt)|(?:za)|(?:zm)|(?:zw))(?:/[^\s()<>]+[^\s`!()\[\]{};:\'".,<>?\xab\xbb\u201c\u201d\u2018\u2019])?)',
        "type": ("dns", "domain")
    },
    {
        "pattern": r"(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)",
        "type": ("ip", "ipv4")
    },
    {
        "pattern": r'((?:(?:\\d{4}[- ]?){3}\\d{4}|\\d{15,16}))(?![\\d])',
        "type": ("cc", "creditcard")
    },
    {
        "pattern": r"[STFG]\d{7}[A-Z]",
        "type": ("nric", "fin", "ic")
    },
    {
        "pattern": r'(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:){1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4}){1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9]))',
        "type": ("ipv6", "ip6")
    },
    {
        "pattern": r"^(?:[A-Za-z0-9+/]{4})*(?:[A-Za-z0-9+/]{2}==|[A-Za-z0-9+/]{3}=)?$",
        "type": ("base64", "b64")
    }
]
```

This solution helped speed up the redaction in two ways. First, the `re.compile` of the regex library which actually takes a lot of time to compile is taken out. Only the needed regular expressions are used to identify the sensitive data within text files. (Reducing the runtime by about 30 seconds)

Secondly, there was no iteration through a list and redaction engine is further optimized and thus processing much faster. This again reduces the runtime I was able to reduce the time from 67 seconds to a mere 1 second for a single file redaction with over 10k lines of records.

```bash
pyredactkit.py ip_test.txt  1.74s user 0.13s system 124% cpu 1.504 total
```

## Multi-file processing bottle neck

```bash
➜  PyRedactKit git:(main) time python pyredactkit.py multiredact -d redacted_dir

    ______       ______         _            _     _   ___ _   
    | ___ \      | ___ \       | |          | |   | | / (_) |  
    | |_/ /   _  | |_/ /___  __| | __ _  ___| |_  | |/ / _| |_ 
    |  __/ | | | |    // _ \/ _` |/ _` |/ __| __| |    \| | __|
    | |  | |_| | | |\ \  __/ (_| | (_| | (__| |_  | |\  \ | |_ 
    \_|   \__, | \_| \_\___|\__,_|\__,_|\___|\__| \_| \_/_|\__|
           __/ |                                               
           |___/                                                                                                           
            +-+-+-+-+-+-+-+ +-+-+ +-+-+-+-+-+-+-+-+-+
            |P|o|w|e|r|e|d| |b|y| |B|r|o|o|t|w|a|r|e|
            +-+-+-+-+-+-+-+ +-+-+ +-+-+-+-+-+-+-+-+-+
            
    https://github.com/brootware
    https://brootware.github.io                                                                             
    
[ + ] redacted_dir directory does not exist, creating it.
[ + ] Processing starts now. This may take some time depending on the file size. Monitor the redacted file size to monitor progress
[ + ] No option supplied, will be redacting all the sensitive data supported

[ + ] Redacted 10038 targets...
[ + ] Took 10.545741081237793 seconds to execute
[ + ] Redacted results saved to redacted_dir/redacted_ip_test 2.txt
[ + ] Processing starts now. This may take some time depending on the file size. Monitor the redacted file size to monitor progress
[ + ] No option supplied, will be redacting all the sensitive data supported

[ + ] Redacted 10038 targets...
[ + ] Took 10.369873046875 seconds to execute
[ + ] Redacted results saved to redacted_dir/redacted_ip_test 4.txt
[ + ] Processing starts now. This may take some time depending on the file size. Monitor the redacted file size to monitor progress
[ + ] No option supplied, will be redacting all the sensitive data supported

[ + ] Redacted 10038 targets...
[ + ] Took 10.294179677963257 seconds to execute
[ + ] Redacted results saved to redacted_dir/redacted_ip_test 3.txt
[ + ] Processing starts now. This may take some time depending on the file size. Monitor the redacted file size to monitor progress
[ + ] No option supplied, will be redacting all the sensitive data supported

[ + ] Redacted 10038 targets...
[ + ] Took 10.296481847763062 seconds to execute
[ + ] Redacted results saved to redacted_dir/redacted_ip_test.txt
python3 pyredactkit.py multiredact -d redacted_dir  42.12s user 0.20s system 100% cpu 41.941 total
```

This is currently on the [TODO list](#todos-and-enhancements) to explore either async or multiprocessing library of python to process the files simultaneously. The issue simply here is that the files are currently being redacted in synchronous manner.

An update as of 10th August 2022, benchmarks have been done on both Async, Threading and Multi-processing. Reading and writing files to disk are already non blocking. The concurrency only comes in handy when the requests are processed over the network. Further explanation can be found in this [issue](https://github.com/brootware/PyRedactKit/issues/1)

# Automating CICD with github actions

As a single dev working on this project, Github Actions are  awesome. There are a lot of pre-built tools available in [github marketplace](https://github.com/marketplace?type=actions). We could just define these tools in a YAML format to do code checkout, linting, testing, code scanning and building up the package.

Currently I have implemented code scanning with sonarqube for detecting any code smells and linting python code according to flake8. There are a couple of todos regarding CD that I have listed out in the [Todos and enhancements](#todos-and-enhancements) section.

# Implementing Unredaction function

This feature is by far the most requested amongst my colleagues. In the first implementation, the original data is lost once you have redacted it and finding back out what was the original data in the log file manually is a very tedious process.

First design consideration to remedy this is to hash and salt the original sensitive data and store them as a `key:value` pair in hashtable(hashmap) format.

```json
{"hash('sensitive data' + salt)" : "sensitive data"}
```

However, this design did not make much sense as this implementation is used more for storing username and password for authentication. At the time of this writing and with the limited test cases I have gone through I have opted to go for using a Universally unique identifier `uuid()` instead to map to sensitive data. So the design looks something like below for the implementation.

```json
{"uuid()" : "sensitive data"}
```

There are a pros and cons to this design.

Pros

* During the redaction, there will be a hashmap of `key:value` pairs generated and stored in a file called `.hashshadow_filename.json` as a dot file together with a `redacted_logfile.txt`. This is a 1:1 generation. You will not be able to use a hashshadow file generated from the copy of an original log file to unredact the original log file.

* The UUID strings generated have a [.00000006 chance of two uuids being the same](https://stackoverflow.com/questions/292965/what-is-a-uuid) according to stackoverflow. So a malicious user will not be able to use a [dictionary attack](https://security.stackexchange.com/questions/67712/what-are-the-differences-between-dictionary-attack-and-brute-force-attack) to bruteforce the original data. Keeping this hashshadow file safe is very important if you need to unredact the data and of course away from the attackers too!

Cons

* The UUID strings generated will sometimes clash with the regular expression of Singapore NRIC or Credit Cards which creates an extra record in `.hashshadow_filename.json` file. Here's an example

![uuidclash](https://bn1304files.storage.live.com/y4mdcXDw9q6clVupqFLERH2u9dsXdoaVS8cIxcKJr0pb0SqMdpBBlz3LEIa_bxZVrl7T95cHp5vd9REOgK6eJnOsVNeDpzvFaZqHoWwZ-bsR3GkZv0VmMtsEC5f6g9v3rpTfZy13l0XNdMTB7eyG3UXHyaMPGVswSgU4j2XSopePuAPfJ74Vc_wBzyTgwaWaRsz?width=1286&height=158&cropmode=none)

The regex actually picks up the valid NRIC pattern from a UUID string and creates an extra record. This is a very rare instance and currently in the limited test case I have gone through, it does not effect the data unredaction functionally. Only when there are trillions of records, I would probably start to see some edge cases.

# Reporting function to show man hour saved

This function is actually a nice cosmetic feature implemented to see how much man hours you would have used if you were to manually look through the log files without any help of regex or text finding tools. The numbers are only estimates and in no way does not represent actual time used by an engineer with knowledge of `regex, grep, sed` and text editing tools.

The report is generated as `manhour_saved_filename.txt` and provides the details as below.

```txt
[ + ] Estimated total words : 30316
[ + ] Estimated total minutes saved : 405
[ + ] Estimated total man hours saved : 6
```

The algorithm behind this is quite simple and it is actually the same as the algorithm behind reading time that you saw earlier at the start of this blog.

```python
reading_minutes = math.ceil(total_words/Word_Per_Minute_Read)
reading_hours = math.floor(reading_minutes/60)
```

# Custom Redaction Engine

The implementation of custom redaction engine is similar to core redaction engine. The core redaction engine has a pre-defined list of regex patterns that will identify and redact data from the text files.

The custom redaction engine on the other hand takes in a set of user defined regular expression patterns to redact sensitive data from text files. The user can define their own custom regex pattern in a json file format as below for pyredactkit to identify and redact during runtime.

```json
// custom.json
[
    {
        "pattern": "^(?:-----BEGIN PGP PRIVATE KEY BLOCK-----\\n?(?:(?:(?:Version|Comment|MessageID|Hash|Charset):.*)\\n?)*[a-zA-Z0-9\\/\\.\\n\\:\\+\\=]+-----END PGP PRIVATE KEY BLOCK-----)$",
        "type": [
            "PGP",
            "Bug Bounty"
        ]
    },
    {
        "pattern": "^(ssh-rsa [A-Za-z0-9+\\/=]+ [^ \\n]+)$",
        "type": [
            "Credentials",
            "SSH Public Key"
        ]
    }
]
```

The usage of this can be found in this [pyredactkit wiki article](https://github.com/brootware/PyRedactKit/wiki/Usage#advance-usage).

# Todos and enhancements

Here is a roadmap of what's in store for PyRedactKit. If you would like to contribute and extend regex identifier database please check out the [guide](https://github.com/brootware/PyRedactKit/wiki/Contributing).

* [x] Refactor regex functions as a class?
* [x] Singapore NRIC
* [x] Performance improvement, 67s -> 1.7s for over 4GB data.
* [x] Reporting function to show how much hours you have saved by using the tool.
* [x] Tokenization for unredacting data
* [x] Base64 supoort
* [x] Custom regex pattern definition for power users
* [x] Implement testing in CI
* [x] Implement building python app and pushing to Pypi in CD
* [x] Dockerise the app for distribution
* [x] Refactoring core and custom redaction engine
* [ ] Multiprocessing files (No longer implementing)
* [ ] UUID clash on hashmap generation?

If you feel that there are suggestions or ideas you have in mind that could enhance the tool please feel free to create an [issue](https://github.com/brootware/PyRedactKit/issues) here to let me know!
