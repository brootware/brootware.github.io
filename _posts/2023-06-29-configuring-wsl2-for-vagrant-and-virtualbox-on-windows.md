---
layout: post
title: Configuring Wsl2 for Vagrant and Virtualbox on Windows
categories:
- Projects
- Automation
tags:
- Projects
- Automation
- Bash
date: 2023-06-29 09:28 +0800
---
# Introduction
I have recently moved back to using windows as the new apple M1 and M2 chips are not compatible with running vagrant and virtualbox. As someone who likes to run labs locally without breaking the bank spending a lot on Apple hardware. I had to find alternatives and being a mac user since 2018, I was overwhelmed with options avaialble in Windows ecosystem. I came across Lenovo's Thinkpad Z16 which is a very close competitor to build quality of a macbook. So I purchased it over the Christmas prior to the GST(Goods Service Tax) hikes in Singapore.

This is a quick blog entry to note down the steps I had gone through to refer back to later in case I need to set it up again.

# Setting up a new machine and automating tasks

Below are the steps in order. Do note that **vagrant versions installed on both Windows Native and WSL2 has to be same** for this to work.
* Enable WSL2 from additional window feature.
* Install Vagrant on Windows and on WSL2 according to your linux distro https://developer.hashicorp.com/vagrant/downloads.
* Check for the version of vagrant installed by running `vagrant -v` in powershell and WSL2.
* Ensure they are the same version.
* If not, go directly to source URL to download the same versions of windows msi and linux package to install locally. https://releases.hashicorp.com/vagrant
  * Windows installation will end with `*.msi` and Ubuntu would be `*.deb`.
* Add these environment variables to either `.bashrc` or `.zshrc`

```bash
echo 'export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1" \nexport PATH="$PATH:/c/Program Files/Oracle/VirtualBox"' >> ~/.bashrc
```

This should set everything up to run vagrant with virtualbox from wsl2 in Windows.

# Source
 - https://developer.hashicorp.com/vagrant/docs/other/wsl