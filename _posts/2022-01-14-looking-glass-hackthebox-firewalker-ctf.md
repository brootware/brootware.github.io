---
layout: post
title: Looking Glass Hack The Box Firewalker CTF 🔎 💉
date: 2022-01-14 15:03 +0800
categories:
- CTF
- Web-exploitation
tags:
- CTF
- Web-exploitation
---
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)

<https://ctf.hackthebox.com/ctf/240>

Point: 325

## Category

Web-Exploitation

## Question

### We've built the most secure networking tool in the market, come and check it out

## Solution

This challenge is a remote code execution vulnerability challenge. You are given a web page to test out web-based networking tools namely ping and traceroute.
The web page is quickly popped in Owasp ZAP to recon the requests and responses to and from the server.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/1.png)

A breakpoint is set to examine the request further.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/2.png)

From the request, we can see that a ping request is sent to the server in the following format.
`test=ping&ip_address=188.166.174.107&submit=Test`

A test injection command is crafted in the command as below to see if the request would be vulnerable to remote code execution on the server.

`test=ping&ip_address=188.166.174.107;id&submit=Test`

Indeed, the web application is vulnerable to an RCE from the response.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/3.png)

Another test command is crafted to further enumerate the context of the environment we're in as below.
`test=ping&ip_address=188.166.174.107;pwd&submit=Test`
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/4.png)

The command again shows that we are in the www directory of the server.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/5.png)

We can see from here that there is a flag file one level up from the www directory of the server.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/6.png)

The final command is crafted further to read the contents of the flag file.
`test=ping&ip_address=188.166.174.107;cd ..;cat flag_Hy10K&submit=Test`
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/7.png)

The output of the contents is then shown in the output pane of the looking glass web application.
![image](https://raw.githubusercontent.com/brootware/CTF-Writeups/master/Web-Exploitation/lookingGlass/8.png)

## Improvement

None

## For more writeups

This write up is featured as just one of many write-ups that has been written. For more writeups please visit this [repo](https://github.com/brootware/CTF-Writeups)
