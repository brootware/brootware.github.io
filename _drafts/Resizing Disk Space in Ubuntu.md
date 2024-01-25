---
layout: post
title: Resizing Disk Space in Ubuntu
categories:
- General-tech
- Misc
tags:
- General-tech

---

## Summary

This blog entry is a tutorial on how you can resize and allocate more storage space on a Debian or Ubuntu virtual machine you are administering in any kind of cloud platform. Be it Azure, AWS or VMWare. Although some cloud providers like Azure allows your to [easily extend the disk space without any downtime](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/expand-disks?tabs=ubuntu#expand-without-downtime).

## Steps

### Allocate more storage to guest machine

Firstly, ensure you have allocated the required storage space to your guest machine according to your cloud provider.


### From within the guest machine

SSH into the guest machine and check for current existing storage space

```bash
lsblk
```

Extend the relative free disk space to current ubuntu file system using the command below. It will allocate all the available free space given to the guest 

```bash
sudo lvextend -r -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

### Verify 

Verify that the storage has been allocated by running `lsblk` again.