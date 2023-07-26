---
layout: post
title: AWS Bucket - Cloud Security ☁️ 🪣 🔐
date: 2022-02-05 17:16 +0800
categories: [CTF, Cloud-Security, Incident-Response]
tags: [CTF, Cloud-Security, Incident-Response, Blue-team]
---

Point: 600

<a href="https://cyberdefenders.org/blueteam-ctf-challenges/progress/rootware/84/"><img src="https://img.shields.io/badge/completed-aws%20cloud%20security-brightgreen.svg" /></a>

## Category

Cloud Security, Blue team, Incident Response

## Challenge Details

Welcome, Defender! As an incident responder, we're granting you access to the AWS account called "Security" as an IAM user. This account contains a copy of the logs during the time period of the incident and has the ability to assume the "Security" role in the target account so you can look around to spot the misconfigurations that allowed for this attack to happen.

Credentials\
Your IAM credentials for the Security account:

- Login: <https://flaws2-security.signin.aws.amazon.com/console>
- Account ID: 322079859186
- Username: security
- Password: password
- Access Key: AKIAIUFNQ2WCOPTEITJQ
- Secret Key: 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

Environment
The credentials above give you access to the Security account, which can assume the role of "security" in the Target account. You also have access to an S3 bucket, named flaws2_logs, in the Security account, that contains the CloudTrail logs recorded during a successful compromise

## Solution

### What is the full AWS CLI command used to configure credentials?

aws configure

### What is the 'creation' date of the bucket 'flaws2-logs'?

```bash

aws s3api list-buckets
{
    "Buckets": [
        {
            "Name": "flaws2-logs",
            "CreationDate": "2018-11-19T20:54:31+00:00"
        }
    ],
    "Owner": {
        "DisplayName": "scott+flaws2_security",
        "ID": "0ff467deaf461e549934997a2df02d29c8010173b1464262782d522bce63bf46"
    }
}
```

The answer to the question is as below date-time format.
2018-11-19 20:54:31 UTC

### What is the name of the first generated event -according to time?

```bash

aws s3 cp s3://flaws2-logs/AWSLogs/653711331788/CloudTrail/us-east-1/2018/11/28/653711331788_CloudTrail_us-east-1_20181128T2235Z_cR9ra7OH1rytWyXY.json.gz . --no-sign-request

cat 653711331788_CloudTrail_us-east-1_20181128T2235Z_cR9ra7OH1rytWyXY.json | jq '.Records[0].eventName'
"AssumeRole"
```

### What source IP address generated the event dated 2018-11-28 at 23:03:20 UTC?

```bash

cat 653711331788_CloudTrail_us-east-1_20181128T2305Z_83VTWZ8Z0kiEC7Lq.json | jq '.Records[0].sourceIPAddress'
"34.234.236.212"

```

### Which IP address does not belong to Amazon AWS infrastructure?

```bash

cat 653711331788_CloudTrail_us-east-1_20181128T2305Z_zKlMhON7EpHala9u.json | jq '.Records[0]'
{
  "eventVersion": "1.05",
  "userIdentity": {
    "type": "AWSAccount",
    "principalId": "",
    "accountId": "ANONYMOUS_PRINCIPAL"
  },
  "eventTime": "2018-11-28T23:02:56Z",
  "eventSource": "s3.amazonaws.com",
  "eventName": "GetObject",
  "awsRegion": "us-east-1",
  "sourceIPAddress": "104.102.221.250",
  "userAgent": "[Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36]",
  "requestParameters": {
    "bucketName": "flaws2.cloud",
    "key": "index.htm"
  },
  "responseElements": null,
  "additionalEventData": {
    "x-amz-id-2": "E/0Pm6UrRvpvNEYDjXhDnCsudplsm91YXPM5ShG/caAJ8H/adUjxO4fTIWRNKLwkVB9G6zEszOI="
  },
  "requestID": "D9DB3DE51407FFEC",
  "eventID": "38993e7c-b9e9-46eb-9f93-82e710147d39",
  "readOnly": true,
  "resources": [
    {
      "type": "AWS::S3::Object",
      "ARN": "arn:aws:s3:::flaws2.cloud/index.htm"
    },
    {
      "accountId": "653711331788",
      "type": "AWS::S3::Bucket",
      "ARN": "arn:aws:s3:::flaws2.cloud"
    }
  ],
  "eventType": "AwsApiCall",
  "recipientAccountId": "653711331788",
  "sharedEventID": "9deca33a-d705-42ef-b425-cafe63c5011c"
}
```

Narrowing down the answer further as below.

```bash

cat 653711331788_CloudTrail_us-east-1_20181128T2305Z_zKlMhON7EpHala9u.json | jq '.Records[0].sourceIPAddress'
"104.102.221.250"
```

### Which user issued the 'ListBuckets' request?

```bash
cat 653711331788_CloudTrail_us-east-1_20181128T2310Z_jQajCuiobojD8I4y.json | jq '.Records[0]'
{
  "eventVersion": "1.05",
  "userIdentity": {
    "type": "AssumedRole",
    "principalId": "AROAJQMBDNUMIKLZKMF64:d190d14a-2404-45d6-9113-4eda22d7f2c7",
    "arn": "arn:aws:sts::653711331788:assumed-role/level3/d190d14a-2404-45d6-9113-4eda22d7f2c7",
    "accountId": "653711331788",
    "accessKeyId": "ASIAZQNB3KHGNXWXBSJS",
    "sessionContext": {
      "attributes": {
        "mfaAuthenticated": "false",
        "creationDate": "2018-11-28T22:31:59Z"
      },
      "sessionIssuer": {
        "type": "Role",
        "principalId": "AROAJQMBDNUMIKLZKMF64",
        "arn": "arn:aws:iam::653711331788:role/level3",
        "accountId": "653711331788",
        "userName": "level3"
      }
    }
  },
  "eventTime": "2018-11-28T23:09:28Z",
  "eventSource": "s3.amazonaws.com",
  "eventName": "ListBuckets",
  "awsRegion": "us-east-1",
  "sourceIPAddress": "104.102.221.250",
  "userAgent": "[aws-cli/1.16.19 Python/2.7.10 Darwin/17.7.0 botocore/1.12.9]",
  "requestParameters": null,
  "responseElements": null,
  "requestID": "4698593B9338B27F",
  "eventID": "65e111a0-83ae-4ba8-9673-16291a804873",
  "eventType": "AwsApiCall",
  "recipientAccountId": "653711331788"
}
```

Narrowing down the answer to username as below

```bash

cat 653711331788_CloudTrail_us-east-1_20181128T2310Z_jQajCuiobojD8I4y.json | jq '.Records[0].userIdentity.sessionContext.sessionIssuer.userName'
"level3"

```

### What was the first request issued by the user 'level1'?

```bash
cat 653711331788_CloudTrail_us-east-1_20181128T2310Z_7J9NEIxrjJsrlXSd.json | jq '.Records[0].eventName'
"CreateLogStream"
```

## Improvement

None

## For more writeups

This write up is featured as just one of many write-ups that has been written. For more writeups please visit this [repo](https://github.com/brootware/CTF-Writeups)
