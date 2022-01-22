---
title: How To Use Git With SSH keys
date: 2018-07-14
author: nhvu1988
category: Coding
tags: [git,ssh]
---

## Use Case

In my use case, I have the existing SSH keys in a `.ssh` directory like this

![](/assets/img/posts/20180717-ssh-folder.png)

I would like to use them to connect to Git repositories such as [Github](https://github.com), [BitBucket](https://bitbucket.org), or [VSTS](https://visualstudio.microsoft.com/team-services/)... using Git tools such as [Git for Windows](https://git-scm.com/download/win), [SourceTree](https://www.sourcetreeapp.com/).

It should be integrated with **CMD Windows** application and some built-in Git clients in Visual Studio and VSCode as well.

## Upload public SSH key to Git repositories

### Github

Go to **SSH and GPG keys** section in your **Github profile** page, click to **New SSH key** button and paste all content in your public SSH key (`nhvu1988.pub` in my example) to textbox **Key** and save. The added key will show like this.

![](/assets/img/posts/20180717-ssh-github.png)

### BitBucket

Go to **SSH keys** section in your **Profile Settings** page, click to **Add key** button and paste all content in your public SSH key (`nhvu1988.pub` in my example) to textbox **Key** and save. The added key will show like this.

![](/assets/img/posts/20180717-ssh-bitbucket.png)

### Do the same with other Git repositories 😉

Just find a **SSH keys** section in your **Profile/Settings** page, click to **Add key** button and paste all content in your public SSH key (``nhvu1988.pub`` in my example) to textbox **Key** and save.

## Tools and Setup

### SourceTree

> It's recommended to use **SourceTree** rather than Git's commands

- **SourceTree**'s a Git GUI client has a very clean, beautiful UI and many powerful features to help you visualize and work on your Git projects.
- Download [SourceTree](https://www.sourcetreeapp.com) and install with default settings.
- Open **SourceTree**, go to **Options** and in the **General** tab, add the path to private SSH key file (\*.ppk).

![](/assets/img/posts/20180717-source-tree-ssh-path.png)

It's done for SourceTree integration, now you can use SourceTree to connect with Git repositories without username/password.

If you `only` want to use  **SourceTree** to work with Git, you can stop here. The next step is for setting up the integration with other clients such as **cmd**, **VSCode** or **VS2017**.

### CMD, built-in Git clients in VSCode and VS2017

- Download [Git for Windows](https://git-scm.com/download/win) and install with default settings.

- If you already installed the **SourceTree**, open **Environment Variables** and create new User variable with **name** is `GIT_SSH` and **value** is the path to `plink.exe` existing in **SourceTree** installed folder.

![](/assets/img/posts/20180717-git-ssh-env-path.png)

Done! You can connect to any reporitory using CMD, built-in Git clients in VSCode and VS2017.

### CMD, built-in Git clients in VSCode and VS2017 **without** SourceTree

- If you didn't install the SourceTree, download [pageant.exe](https://the.earth.li/~sgtatham/putty/latest/w64/pageant.exe) and [plink.exe](https://the.earth.li/~sgtatham/putty/latest/w64/plink.exe).

- Run `pageant.exe` and add SSH key file (\*.ppk) to pageant key list

![](/assets/img/posts/20180717-add-ssh-pageant.png)

- Open **Environment Variables** and create new User variable with **name** is ``GIT_SSH`` and **value** is the path to downloaded ``plink.exe``.
- Done! You can connect to any reporitory using CMD, built-in Git clients in VSCode and VS2017 without SourceTree.

### To make Pageant automatically run and load keys at Windows startup

- Find the location of pageant.exe
- Press `Windows` + `R` to open the 'run' dialog box
- Type `shell:startup` in the dialog box
- Create a shortcut to the pageant.exe and put into this startup folder.
- Right click on the shortcut and open 'Properties'
- In **Target** add: `<path to>/pageant.exe` myprivatekeyname.ppk
- In **Start in** add: `<path to *.ppk>`
- Click on the shortcut link and check that Pageant has started and has loaded your keys
