---
layout: post
title: Resizing Disk Space in Ubuntu
categories:
- General-tech
- Misc
tags:
- General-tech
- Misc
- task-scheduler
- cron-jobs
---

## Summary

Check for current existing storage space

```bash
lsblk
```

extend/allocate all the free disk space to current ubuntu file system.

```bash
sudo lvextend -r -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```
