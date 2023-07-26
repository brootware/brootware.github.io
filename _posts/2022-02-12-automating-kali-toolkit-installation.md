---
layout: post
title: Automating kali toolkit installation 🧰
date: 2022-02-12 17:53 +0800
categories: [Projects, security-toolkit]
tags: [Projects, security-toolkit, Ansible, Automation, DevOps]
---
## Introduction

As security professionals, it could be very time consuming to spin up a kali machine with all the tools needed to do any form of Red or Blue team work like bug bounty, threat hunting and malware analysis or even to play Capture The Flag (CTF) competitions.

>TLDR: I just need a kali up and running FAAAAAST!! -> <https://github.com/brootware/kali-up>

To embark on this project, here is a checklist of things borrowed from the Infrastructure-As-Code (IAC) philosophy of DevOps to fit my needs. The automation tool has to be:

- Portable across platform. It has to be distro agnostic, able to run anywhere: A workstation or a VM running Linux.
- Has to be extensible. More tools can be added and more customization can be done like adding a vagrant file with a provisioner to spin up a VM with all the tools and dependency installed with one command line.
- Idempotence to easily manage versioning of security testing tools within the automation.

## Finding any existing open source projects

So I looked around for any open source projects that might fit this need and came across a couple of interesting projects which is [disposable-kali](https://github.com/stevemcilwain/Disposable-Kali) by stevemcilwain and a project called [kali-up](https://github.com/archcloudlabs/kali-up) by archcloudlabs.

### Disposable-kali (Bash script approach)

For [disposable-kali](https://github.com/stevemcilwain/Disposable-Kali) the installation components in the [setup script](https://github.com/stevemcilwain/Disposable-Kali/blob/master/scripts/setup.sh) is spinning up a fresh VM to install all the additional tools with customizations which fits my needs.

However, the project did not meet the three points mentioned earlier as it is running a bash script to set up all the tools which are also distro specific (debian based) and not able to run on any other distros like Fedora or Suse.

### Kali-up (Ansible approach)

[kali-up](https://github.com/archcloudlabs/kali-up) project was fairly simple when I discovered it. The setup is done via ansible roles which fits all three requirements. Each ansible roles contain a collection of security testing tools that gets installed.

- [x] Portable
- [x] Extensible
- [x] Idempotence

You simply have to clone the repo and run `ansible-playbook site.yml` to install all the tools locally. But I ran into a fair bit of errors as some of the roles have broken links for installation. The project also does not have the option to install all these on a fresh VM.

## Combining best of both worlds (Why not both?)

So I forked the repo and added in further customizations to fit my needs. You can find my version of the [kali-up](https://github.com/brootware/kali-up) <--- here.

- [x] Fixed broken roles for installation
- [x] Refactored all the roles for easier versioning.
- [x] Added Vagrant to provision a VirtualBox VM with an ansible provisioner to install all the roles.
- [ ] (TODO) Translate installation components from [disposable-kali](https://github.com/stevemcilwain/Disposable-Kali) installation script into roles and add in to my version of [kali-up](https://github.com/brootware/kali-up).

## A quick guide on installing Ansible roles on VMWare Fusion for Mac Users

This is specifically for Mac users who are using VMWare Fusion for kali.

1. Download and install [VMWare Fusion Personal Use](https://customerconnect.vmware.com/web/vmware/evalcenter?p=fusion-player-personal).

2. Download [kali machine](https://www.kali.org/get-kali/#kali-virtual-machines).

3. Import the downloaded kali machine into VMWare Fusion and spin up the VM.

4. Log in to the kali VM and Clone this repo.

   ```bash
   git clone https://github.com/brootware/kali-up.git && cd kali-up
   ```

5. Modify [site.yml] to have the Ansible roles you want to install on your machine by commenting. Else all the roles will be installed.

   ```yaml
   roles:
     - c2-frameworks
     - re-frameworks
     - pwn-windows
     - pwn-linux
     - install-ghidra
     - chown-dirs
     - forensics-blue
   ```

6. Execute the following if you are installing it without any virtualization.

   ```bash
   ansible-playbook site.yml
   ```

Note that this is just one of the options to install and if you want a more detailed installation guide you can visit the repository at <https://github.com/brootware/kali-up> .

## Conclusion

Thank you for reading. If this guide helped you in any way, please share around. If you run into any trouble with the installation make sure to open an issue at <https://github.com/brootware/kali-up/issues>.
