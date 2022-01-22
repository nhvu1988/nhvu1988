---
title: Use OpenSSH in Windows
date: 2021-03-14 07:00:00 +0700
author: nhvu1988
category: DevOps
tags: [openssh,windows]
---

Sometimes I manage some stuffs using [OpenSSH](https://www.openssh.com/) such as convert certificate file from .key format to .pfx format. But it's hard to find an official binary or installer (at least for me whenever I find it 😂).

> There's a simple way to use OpenSSH on Windows if (already?) having [git](https://git-scm.com/download/win) installed on the machine.

Go to the `C:\Program Files\Git\usr\bin\` folder and run cmd from there. You can also run from [git bash](https://www.atlassian.com/git/tutorials/git-bash) as well.

There're a lot of developer tools here including `openssl.exe`.

An example command to convert PEM certificate to PFX certificate.

```bash
openssl pkcs12 -export -out "C:\Users\nhvu1988\Desktop\cert.pfx" -inkey "C:\Users\nhvu1988\Desktop\cert.key" -in "C:\Users\nhvu1988\Desktop\cert.pem"
```

That's easy right? 😎 Happy coding everyone!