---
title: How I install git on Windows
date: 2020-07-26 07:00:00 +0700
author: nhvu1988
category: DevOps
tags: [git,windows]
---

When installing git on Windows, we need to do some steps for installation and setup before working with it so I will keep track them below as well as the reason why I do that. If you have any suggestion to improve it, I'm very appreciated to know and learn to make it better.

Since I will clone git using SSH as the article [How to use git with SSH key](/posts/how-to-use-git-with-ssh-key/) so my setup will make sure it works with SSH as well.

## Download and install Git client

1. Go to [Git-scm](https://git-scm.com/download/win) and download the latest 64-bit version.
2. Start the setup by double click the installer.
3. Click next on first screen and in the `Select component` screen. I uncheck to `Windows Explorer integration` and `Associate .sh files...` because I don't use those features and put more things to my mouse-right-click menu.

    ![](/assets/img/posts/2020-07-26_16-25-51.png)

4. Click next until the `Choosing the default editor used by Git` screen. I change it to `Use Notepad++ as Git's default editor` instead, since I would like to use Notepad++ because it's light and fit to this job.

    ![](/assets/img/posts/2020-07-26_16-37-27.png)

5. Click next and go to `Adjust your PATH environment` screen and keep the recommended option because as descibed, it makes Git works well with any tools.

    ![](/assets/img/posts/2020-07-26_16-39-42.png)

6. Click next and in the `Configuring the line ending conversions` screen, I choose the `Checkout as-is, commit as-is` since I don't want Git to do any conversion when doing checkout/commit.

    ![](/assets/img/posts/2020-07-26_16-47-08.png)

    This option could be overrided later with git command
    >   `$ git config --global core.autocrlf false`

7. Click next and in the `Configuring the terminal emulator to use with Git Bash`, I choose to `Use Windows' default console window` because as I said before, I don't want to install any other terminal. Actually, I use other tools (PHPStorm and Windows Terminal) for this job so just keep thing light 😉

    ![](/assets/img/posts/2020-07-26_16-59-23.png)

8. Click next and in the `Choose the default behavior of git pull`, I leave it as default (fast-forward or merge)
9. Click next and in the `Configuring extra options`, I uncheck the `Enable Git Credential Manager` because I use SSH so no need to use this. But we should keep it if want to HTTPS and enable multi-factor authentication.

    ![](/assets/img/posts/2020-07-26_17-05-59.png)

10. Click next and install button in the last screen. I don't want to try any experience feature because it's working environment and should be stable as much as possible.

## Setup default email and user name

Before doing any `git commit`, we need to setup git username and email with the commands below

```bash
$ git config --global user.name "Vu Nguyen"
$ git config --global user.email nhvu1988@gmail.com
```

It's possible to config the specific email in a single repository. In the repository folder, run the following git config command

```bash
$ git config user.email nhvu1988@working.mail
```

## Setup SSH key

This step could be simple or complicated due to what we need, such as my requirement is 

> Use only one SSH key for all machines.

So I don't have to manage the SSH key by creating a new one for every machine, and remember to remove it when not used anymore.

### Prepare SSH key

1. Create an SSH key following [this guide](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) from github. 
2. Upload SSH key to git repositories following [my guide](/posts/how-to-use-git-with-ssh-key/).

### Configure SSH key

1. Create a `config` file inside `~/.ssh/` folder. The folder is `C:\Users\nhvu\.ssh\` on my machine but different username on yours.

    > The file has no extension, remember removing `.txt` or anything else if creating using Windows Explorer.

2. Add the content below and update `~/.ssh/id_rsa` to your SSH key file path or keep it if you just create a new one.

    ```bash
    host *
      IdentityFile ~/.ssh/id_rsa
    ```

3. (Optional) This step is not for me but for someone want to have specific SSH key for each host, you can add more record to the `config` file.

    ```bash
    host github.com
      IdentityFile ~/.ssh/id_rsa.github

    host gitlab.com
      IdentityFile ~/.ssh/id_rsa.gitlab
    ```