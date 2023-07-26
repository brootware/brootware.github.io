---
layout: post
title: Lespion OSINT Challenge 🕵 🔎
categories:
- CTF
- OSINT
tags:
- CTF
- OSINT
date: 2022-02-17 18:45 +0800
---
Point: 850

<a href="https://cyberdefenders.org/blueteam-ctf-challenges/progress/rootware/73/"><img src="https://img.shields.io/badge/completed-lespion%20opensource%20intelligence-brightgreen.svg" /></a>

## Category

OSINT - Open Source Intelligence

## Challenge Details

You have been tasked by a client whose network was compromised and brought offline to investigate the incident and determine the attacker's identity.

Incident responders and digital forensic investigators are currently on the scene and have conducted a preliminary investigation. Their findings show that the attack originated from a single user account, probably, an insider.

Investigate the incident, find the insider, and uncover the attack actions.

## Solution

### 1

File -> Github.txt: What is the API key the insider added to his GitHub repositories?

```bash
cat Github.txt                           
https://github.com/EMarseille99   
```

By enumerating further on a javascript custom [login page project](https://github.com/EMarseille99/Project-Build---Custom-Login-Page/blob/master/Login%20Page.js) that this github user is working on, we can find the API Key within the code.

```javascript
API Key = aJFRaLHjMXvYZgLPwiJkroYLGRkNBW

<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<title>File Cube's BCFT System</title>

    <link rel="icon" type="image/png" href="images/icons/favicon.ico"/>
    <link rel="stylesheet" type="text/css" href="vendor/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="fonts/font-awesome-4.7.0/css/font-awesome.min.css">
    <link rel="stylesheet" type="text/css" href="vendor/animate/animate.css">
    <link rel="stylesheet" type="text/css" href="vendor/css-hamburgers/hamburgers.min.css">
    <link rel="stylesheet" type="text/css" href="vendor/select2/select2.min.css">
    <link rel="stylesheet" type="text/css" href="css/util.css">
    <link rel="stylesheet" type="text/css" href="css/main.css">

    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
</head>
```

API_KEY=aJFRaLHjMXvYZgLPwiJkroYLGRkNBW

### 2

File -> Github.txt: What is the plaintext password the insider added to his GitHub repositories?

```javascript
<!-- 
This .jsp calls the "UserProfile" servlet at the point in which the user 
input's their username and password into the form's text fields
-->

<form class="login100-form validate-form" action="UserProfile" method="post">

<div class="wrap-input100 validate-input" data-validate = "Valid username is required: FileCubeUser100">

    Username: EMarseille99
    
    <span class="focus-input100"></span>
    <span class="symbol-input100">
    <i class="fa fa-chain" aria-hidden="true"></i>
    </span>
    
</div> 


<div class="wrap-input100 validate-input" data-validate = "Password is required">

    Password: UGljYXNzb0JhZ3VldHRlOTk=
    Password(base64)
    
    <span class="focus-input100"></span>
    <span class="symbol-input100">
    <i class="fa fa-lock" aria-hidden="true"></i>
    </span>
</div>

<div>
    <input class="login100-form-btn" type="submit" value="Login">
</div>

</form>

</body>
```

From the same javascript [login page project](https://github.com/EMarseille99/Project-Build---Custom-Login-Page/blob/master/Login%20Page.js), we can find the password that is encode in base64. We can simply decode as follow and get the flag.

```bash
echo 'UGljYXNzb0JhZ3VldHRlOTk=' | base64 --decode
PicassoBaguette99
```

### 3

File -> Github.txt: What cryptocurrency mining tool did the insider use?

By enumerating the EMarseille99's public repositories further, we found an interesting repo called xmrig that is used for crypto mining.
<https://github.com/EMarseille99/xmrig>

### 4

What university did the insider go to?

For this question you will need to search linkedin. I searched for last name and the title keywords as filters below.

![linkedin](https://bn1304files.storage.live.com/y4mqzeMdsX1BFbOYzTklIrrFrpbrRnf26jhRwFJVNEWPKFJryjwGIproEv8FMB8c2NeAz_Ktt6P7KiZGQ0vjxQMwZIH-PSuLaV-nQZKpIDRpEfd4D3X7DLZxUAUtGdvyNwT8rTqcPjB6oj4vuKUzgPTUM_L0dVD_Wqs6LksS8hXM61r5yV72uK_VcX__OO9bp4f?width=1236&height=479&cropmode=none)

By visiting the [linkedin profile](https://www.linkedin.com/in/%C3%A9milie-marseille-4b353a1aa/), we can see that she has gone to Sorbonne University.

### 5

What gaming website the insider had an account on?

By searching for the username **Emarseille99** on google, we can find the instagram profile of the user. The user has an [image of QR code](https://www.instagram.com/p/CAs_zj6FwMT/) that links to steam user id.

### 6

What is the link to the insider Instagram profile?

<https://www.instagram.com/emarseille99/>

### 7

here did the insider go on the holiday? (Country only)

From the instagram profile, we can see that the insider has gone to Singapore for holiday.

### 8

Where is the insider's family live? (City only)

The insider has uploaded 2 photos of Dubai on the instragram profile

### 9

File -> office.jpg: You have been provided with a picture of the building in which the company has an office. Which city is the company located in?

From the image we can see that we are close to 2 theatres

1. Hippdrome
2. Alexandra

By [searching for these 2 theatres](https://www.google.com/search?q=hippodrome+theatre+alexandra+theatre&client=safari&rls=en&sxsrf=APq-WBsbNhjDMPu1ek5yMi4-Kwr9ISylsw%3A1645094350372&ei=ziUOYpCqFovbz7sPyq6ouAU&ved=0ahUKEwiQyYCDxob2AhWL7XMBHUoXClcQ4dUDCA4&uact=5&oq=hippodrome+theatre+alexandra+theatre&gs_lcp=Cgdnd3Mtd2l6EAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsAMyBwgAEEcQsANKBAhBGABKBAhGGABQAFgAYNQFaAFwAXgAgAEAiAEAkgEAmAEAyAEIwAEB&sclient=gws-wiz) we can identify that the office is in Birmingham.

### 10

File -> Webcam.png: With the intel, you have provided, our ground surveillance unit is now overlooking the person of interest's suspected address. They saw them leaving their apartment and followed them to the airport. Their plane took off and has landed in another country. Our intelligence team spotted the target with this IP camera. Which state is this camera in?

**Indiana**

The camera is 24 hour stream from [the University of Notre Dame](https://dome.nd.edu). By checking the address:

`Notre Dame, IN 46556 USA`

 of the university we can identify that the camera is in Indiana state.
