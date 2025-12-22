---
layout: post
title: Starting the great singapore homelab
categories:
- General-tech
- Home-lab
tags:
- General-tech
- Virtualization
- Proxmox
date: 2025-10-08 16:33 +0800
---
# Introduction

This is a quick update of this blog as I have not been been posting much lately on getting involved with Capture The Flag (CTF) competitions. 

![goodnews!](https://media.tenor.com/AyjdnbCSflEAAAAM/good-news-everyone-hubert.gif)

I've decided to start [The Great Singapre Homelab](https://www.youtube.com/@greatsghomelab) Youtube Channel and [Blog](https://greatsghomelab.github.io) . Very often when i come across a weird situation in my homelab, I would dump all the notes into Anytype (An obsidian like peer to peer note taking app). In a blink, I've buit up a library of Knowledge Base that I feel like I've been gate keeping. So this is my way of sharing information and "gotchas" I faced at both work and in the lab. 

## How did I get into homelab?

Long story short: I needed a personal lab environment that I can run 24/7 at home and is free of cloud subscriptions to prepare for the Giac Certified Intrusion Analyst exam. The lab has to be power efficient, small and should not take up too much space.

## So you want to start 1 too?

Well, that's the point of <https://greatsghomelab.github.io>. These are mostly transcript to the videos I'm making on the Youtube Channel. 

It could get overwhelming when you first start out. So my recommendation is if you have an old PC lying around at home: That's where you start!

Usually the hardware components consist of:
- A good router that helps you do VLAN segmentations.
- Optional: A switch if you're planning to build a clustered homelab like me
- A backup laptop to flash the ISOs
- A thumbdrive to flash the ISOs to boot from
- Last but not least an old PC that you don't have much use for anymore

Now comes the software components:
- Proxmox ISO

That's it. that's all you need. Proxmox is a really nice virtualization software based on Debian that comes built in with a web management UI for administration.

I won't go into details about installing proxmox on your PC as ther are already plenty of tutorials online if you just search for keywords like:

```
Install proxmox homelab
```

You can also check out the official documentation at <https://www.proxmox.com/en/products/proxmox-virtual-environment/get-started>

## Footnote

While I'll still be keeping this blog, I will have less time to update it. So you'll see me more active at [The Great Singapre Homelab](https://greatsghomelab.github.io)!

![That's all folks!](https://cdn.looneytuneswiki.com/thumb/3/31/That%27s_all_folks%21_%28LTC%29.png/640px-That%27s_all_folks%21_%28LTC%29.png)
