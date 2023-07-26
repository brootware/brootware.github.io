---
layout: post
title: "Privacy is sexy \U0001F351\U0001F346"
categories:
- Projects
- security-toolkit
tags:
- Projects
- security-toolkit
- Automation
date: 2022-03-03 19:45 +0800
---
## Introduction

As a technology professional, I am agnostic when it comes to operating systems. The only thing I am concerned about is how the hardware and tools I use will help me be productive and at the same time protect my data and privacy in an increasingly surveillent society. For today I will be going through my experience of creating a CLI app to automate my security and privacy hardening process, challenges faced and how they're resolved.

> You can check out the project here <https://github.com/brootware/privacy-sexy-lite>

## Finding any existing open source projects

So I have both Windows and MacOS platforms running on my devices and virtual machines that I will need to do security and privacy hardening on personally. Just like any other tech professional with a github account, I searched for any existing tools that I could use to not reinvent the wheel. Usually a lot of time are lost and progression is slow if you do not know [How to stand on shoulders of giant](https://www.freecodecamp.org/news/how-to-stand-on-shoulders-16e8cfbc127b/). A recommended read by Quincy Larson, freecodecamp's founder.

## A privacy.sexy web app

After searching around, I come across a project called [privacy.sexy](https://privacy.sexy). A web application with really nice user interface for normal everyday users. The users can go to the web page, find security features explained in plain english that they can turn on or turn off using batch (for Windows) and bash (for MacOs) scripts. The commands are shown to the user through a Web UI as below.

![privacy](https://bn1304files.storage.live.com/y4m__zDGxSSnWqMd9bHqBgki23x0NllXa3rQxVyv50LTsDUt84fTRSGpqx40tr8-kGz4H6zwoVlpp-bYHmmE7AQfJH-D__c7S_mDnHslYDh8V5U9LFzkdp1wN_kXnskoUaWffTLDGE_AvXmbC53jO4rv2fMn9uBCjhlHMNegiwH1U0sD9vlvhxhhEdDOsNjR2-I?width=2720&height=1512&cropmode=none)

There is also an desktop version of the app avialable for download that you can view the scripts offline. However, the web app is intended for users to manually run the script one at a time.

## Turning it into cli app for Mac and Windows

Being a power user with multiple sandbox VMs I want to run these scripts on, it made sense for me to wrap all the standard recommended settings in a cli app that I can provision through vagrant. Simply put the cli app has to be :

- [x] Portable. (Cross platofrm for both Mac and Windows)
- [x] Group and modularize standard recommended hardening functions
- [x] Easily invoked via command line interface for automation
- [x] Should not break any systems

> However I'd still advise you to use with caution as different machines could have different effect after running the commands.

So a Bash script cli app for MacOS and Powershell script cli app for Windows is developed to call the recommended functions for security and privacing hardening.

## Challenges faced and how they're resolved

As I started developing, I come across a few challenges.

Firstly the initial script was very monolithic. All of the functions for privacy cleanup, security hardening were all contained in 1 script. Check out the [commit history](https://github.com/brootware/privacy-sexy-lite/commit/b59d1d3398cbe180b28bf089b538328321f0fa1c#diff-78cab4b59ce0ad211b75d3bd17bbf1eec2bede97ae38cec7c66b878e7d7ad69b) here. From experience this could cause headaches in the future.

To remedy this and make the codebase easier to maintain, all of the functions are separated into different source files. (*Yeah I guess this wasn't even a big challenge. But an insight I wanted to share nevertheless.*)

The second challenge is more interesting. Here is the snippet of a windows command that clears up Steam's (a game store application) logs and traces.

```batch
:: ----------------------------------------------------------
:: -----------Clear Steam dumps, logs, and traces------------
:: ----------------------------------------------------------
echo --- Clear Steam dumps, logs, and traces
del /f /q %ProgramFiles(x86)%\Steam\Dumps
del /f /q %ProgramFiles(x86)%\Steam\Traces
del /f /q %ProgramFiles(x86)%\Steam\appcache\*.log
:: ----------------------------------------------------------
```

From the snippet above, the code is written in batch for hardening windows. By the time I noticed it I had already written a [skeleton powershell app](https://github.com/brootware/privacy-sexy-lite/commit/d7a89a54b6cdd862987bd4299dd5c8260704aae5#diff-10126262829c326f04f356abf49c3c8d3ae2d1024a048e0d39e7dace120db29f).

```batch
:: ----------------------------------------------------------
:: --------------Disable Google update service---------------
:: ----------------------------------------------------------
echo --- Disable Google update service
schtasks /change /disable /tn "GoogleUpdateTaskMachineCore"
schtasks /change /disable /tn "GoogleUpdateTaskMachineUA"
PowerShell -ExecutionPolicy Unrestricted -Command "$serviceName = 'gupdate'; Write-Host
```

Some snippets are also invoking powershell from batch! I was stuck for a couple of days and took some time off to think about solving this. (Also quite lazy to rewrite the app in batch) After a few days of researching and asking around the Powershell discord community, I found a way to invoke all the batch functions directly from Powershell. Something like below.

```powershell
function remove_bloatware {
    & '.\batch_scripts\remove_bloatware.bat'
}
```

With this solution, I have grouped all the common functions and made it a lot more modular. Below is a screenshot of a cli app. A user is able to choose what he or she wants to do from the menu.

![cliapp](https://bn1304files.storage.live.com/y4maV5h8FFlltrlEPLneNoOWiHXIyDJ3LL-vf_Exjgq2P4enQzqzBfQyJwmHI19S_-7sawppX1nIab5y9hEKNYFTDxZ9MHZEpkj3g_DU_MYaaQclDVaW_S-NdNshXbKUAd4YwdnsH7fbH24ODwHEappm51_Ifr3dXPGRKcK3ykkC8AwPASXWg36sLsvkKT41Ssp?width=1720&height=720&cropmode=none)

Arguments can be passed to the app too to run the security hardening directly from commandline

![cliargument](https://bn1304files.storage.live.com/y4mXntt0zNQCxnQVdSpHV-1JShM25y19SO-C6bxMB0nUPlzoO0Fhsz-Zsp1HsKiav6cgec9mycYAM2pc2M1yENpGa0Ubd2fP0z-CoNgjJ8zNqnMkC0nJaIZ5cfRWJjlTbLSdlNOEuPx6oERXzNJLGWqPFvsMrk4b36F5fi_SoOtA223OP58OJNXSeKX_iV2ZxLd?width=1244&height=342&cropmode=none)

## Conclusion

Thank you for reading this short blog, please do give the app a try at <https://github.com/brootware/privacy-sexy-lite>. And if you ran into any issues, please log [it here](https://github.com/brootware/privacy-sexy-lite/issues).
