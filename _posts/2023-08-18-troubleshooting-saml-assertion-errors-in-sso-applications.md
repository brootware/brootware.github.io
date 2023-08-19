---
layout: post
title: Troubleshooting SAML assertion errors in SSO applications
categories:
- General-tech
- Web-applications
tags:
- General-tech
- Web-applications
- Single-Sign-On
- SAML
- Troubleshoot
date: 2023-08-18 18:09 +0800
---
## Introduction

Security Assertion Markup Language (SAML) is an open standard for exchanging authentication and authorization data between parties, in particular, between an identity provider and an application or a web service. It is the backbone of Single Sign On (SSO) authentication to an application. There are a lot of identity providers in the market. The general flow of an authentication process amongst them is more or less the same described in the below flowchart from Okta.

![SamlFlow](https://onedrive.live.com/embed?resid=3ac474c28157d633%21343259&authkey=%21ANCLchohsvi7aG4&width=793&height=414)

## Troubleshooting

This blog is going to be focusing on a scenario where as a security admin you're troubleshooting SAML request and assertion errors related to TLS certificates. This corresponds to steps 2 through 6 of the authentication flow as highlighted below.

![SamlHighlight](https://onedrive.live.com/embed?resid=3ac474c28157d633%21343270&authkey=%21ANjGxaT8kBOAFnk&width=790&height=412)

We will be going through how we can capture and look for the SAML request via the browser's developer console to examine the request.

A general overview of the capture process looks like below.

1. Open the browser's developer console --> Network Tab and check on persist/preserve logs.
2. Go to the application's SSO authentication page.
3. Search for `SAMLRequest:` keyword in the developer console.
4. Copy base64 encoded value and decode. The decoded value will be in XML format
5. Search for `<ds:X509Certificate>` tag, copy the value and save it as `troubleshoot.cer` to examine the TLS certificate.

### Searching for SAMLRequest: in the developer console

Ensure you have opened your developer console first and the "preserve logs" checkbox is checked before navigating to the application's SSO authentication page. Search for `SAMLRequest:` keyword with `CTRL + F`.

Look for the `Payload` tab and you will see the `SAMLRequest:` with a large text chunk in base64 encoded format.

![Samlrequest](https://onedrive.live.com/embed?resid=3ac474c28157d633%21343264&authkey=%21AIb4XWtCc-RwoyU&width=1056&height=761)

### Decoding the SAMLRequest:

Right-click on the `SAMLRequest:` payload --> click on `copy value`` and paste it into the text file. Decode the base64 encoded value by running the commands below. The decoded value will be in XML format.

```bash
vi samlrequest.txt
cat samlrequest.txt | base64 -d > samlrequest.xml
```

### Searching for the TLS certificate within the request for analyzing
Open the XML file in Notepad or the text editor of your choice and search for XML tag `<`ds:X509Certificate>`. You will see the base64 encoded value again.

![xmlfile](https://onedrive.live.com/embed?resid=3ac474c28157d633%21343268&authkey=%21ABuJCPsOEVVlZW0&width=1396&height=627)

Copy the encoded value --> Paste it in a new text file and save the file as a certificate. (e.g. `troubleshoot.cer`).
This will allow you to examine the TLS certificate file.

## References

https://developer.okta.com/docs/concepts/saml/#planning-for-saml
https://support.okta.com/help/s/article/How-to-View-a-SAML-Response-in-Your-Browser-for-Troubleshooting?language=en_US