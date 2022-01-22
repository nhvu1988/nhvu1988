---
title: How to fix error HRESULT = '8000000A' when building VS Installer
date: 2020-01-03
author: nhvu1988
category: Coding
tags: [vs installer]
---

It's already answered here [stackoverflow](https://stackoverflow.com/questions/8648428/an-error-occurred-while-validating-hresult-8000000a) but I want to take note for later usage in case I forget it again 🤣. 

> Unfortunately we couldn't address all cases of the command line issue for this release as we're still investigating the appropriate way to address them. What we do have is a workaround that we believe will work for almost all of them. 
>
> If you are still suffering this issue then you can try to change the DWORD value for the following registry value ``EnableOutOfProcBuild`` to ``0``:
>
>   * VS2013
>
>         HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\12.0_Config\MSBuild
>   * VS2015
>
>         HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\14.0_Config\MSBuild
>
> If this doesn't exist you can create it as a DWORD.
>
> [kristian mo](https://stackoverflow.com/a/25054572/5725352)