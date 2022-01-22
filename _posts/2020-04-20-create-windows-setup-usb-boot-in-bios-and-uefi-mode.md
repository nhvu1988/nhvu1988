---
title: Create Windows Setup USB boot in BIOS and UEFI mode
author: nhvu1998
date: 2020-04-20 07:00:00 +0700
category: DevOps
tags: [bios,uefi]
---

![Create Windows boot USB using Rufus](/assets/img/posts/2020-04-20_10-14-33.jpg)

As we know most new PC/laptops are using UEFI mode when booting into Windows Setup but I would like to support the legacy BIOS as well because sometimes I will support to help my mom and others are using the old PCs. So I will create a Windows Setup USB supports to run on any both of them using [Rufus](https://rufus.ie/)

1. Download Windows ISO from Microsoft.
2. Download [Rufus](https://rufus.ie/downloads/){: target="_blank"}.
3. Run Rufus and in the `Partition scheme`, select `MBR`.
4. Make sure the `Target system` change to `BIOS (or UEFI-CSM)`.
5. Click `START` to create the USB boot 😊
