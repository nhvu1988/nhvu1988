---
title: How to create .msi installer using VS Installer
date: 2019-12-09
author: nhvu1988
category: Coding
tags: [vs installer]
---

The VS installer extension is very old, out-dated and obsoleted by Microsoft but it's still the quick and simple way to create an installer for any project. Since I just finished the task relates to it and have to fixed few (or a lot... 😂) issues so this guide is the notes for them.

## Install Visual Studio Installer extension

Open **Visual Studio** -> **Tools** -> **Extensions and Updates** -> **Online** and search for **installer**

![](/assets/img/posts/2019-12-14_10-28-25.png)


## Create Installer project for Windows application

Open **Visual Studio** -> **File** -> **New Project** -> **Other Project Types** -> **Visual Studio Installer** -> **Setup Wizard**

![](/assets/img/posts/2019-12-14_10-38-37.png)

Click **Next** and select **Project type** suits to your application type, mine is Windows application so I choose **Create a setup for a Windows Application**

![](/assets/img/posts/2019-12-14_10-40-13.png)

Click **Next** and in the step **Choose files to include**, we can add any optional files (readme, changelogs...) here. After that, click **Finish** to create the project

## Add application output to Installer

In the Installer project, right click to project name -> **View** -> **File System**

![](/assets/img/posts/2019-12-14_10-46-10.png)

In the **File System on Target Machine** -> **Application Folder** -> **Add** -> **Project Output** and select application project, it will automatically add all files in **bin** folder of your project build to the installer and it will copy them to the application folder while installing.

## Create custom dialog to allow user edit configuration variables

The example is we would like to let user update the connection string of a SQL server database, it requires user to fill a database connection string (we assume that user know how to get it ✌)

Since it's a complex process so I separate it to few small steps below

### Create custom dialog to get input values from user

Right click to **Installer Project** -> **View** -> **User Interface**

![](/assets/img/posts/2019-12-14_10-46-10.png)

In the **Install** section, right click to **Start** -> **Add Dialog**

![](/assets/img/posts/2019-12-14_11-09-08.png)

In the **Add Dialog** popup, choose a suitable dialog for your requirement, I will pick the **Textboxes (A)** since we need to get input string only

![](/assets/img/posts/2019-12-14_11-16-22.png)

> The **Textboxes (A)** will appear at the end of dialogs list, we have to move it to _before_ **Installation Folder** dialog (and _after_ **Welcome** dialog). Otherwise we will get an error during build process.

![](/assets/img/posts/2019-12-14_11-28-01.png)

### Customize the custom dialog

Right click to the **Textboxes (A)** and select **Properties Windows** to open **Properties** windows.

In **Properties** windows, we disable all textboxes except the first one (that use for user inputing the connection string) by set the **Edit2Visible**, **Edit3Visible** and **Edit4Visible** to `False`.

We change the **Edit1Label** to `Database ConnectionString` to make sure user know what's value to input.

You can change the value of **Edit1Property** property to anything but I just let it as default (`EDITA1`) and remember to use it later.

![](/assets/img/posts/2019-12-14_11-39-07.png)

This is the final custom dialog in installation process.

![](/assets/img/posts/2019-12-14_11-40-29.png)

### Create new custom action project

Since the Visual Studio Installer only helps with the custom dialogs or custom actions during installation. We have to create a custom action (library) project to make a simple code to get the user input and update the **App.config**.

In **Visual Studio** -> **File** -> **New Project** -> **Class Library**

![](/assets/img/posts/2019-12-14_11-47-40.png)

Add new **Installer Class** item

![](/assets/img/posts/2019-12-14_11-48-57.png)

In the new CustomInstaller class, we add the override **Install** function likes this

![](/assets/img/posts/2019-12-14_11-53-02.png)

### Add the custom action project to Installer project

Right click to installer **Project Name** -> **View** -> **Custom Actions** -> **Install** -> **Add Custom Action...**

![](/assets/img/posts/2019-12-14_12-00-33.png)

Select **Application Folder** -> **Add Output...** -> **Primary output from custom action project name**

![](/assets/img/posts/2019-12-14_12-02-58.png)

Now, the code in **Install** function will be run during install process. If you want to do the custom action during uninstall process, do the same step 3 and 4 but change to override the **Uninstall** function and add the primary output to **Uninstall** section.

### Update custom action data with user input and default installation data

Right click to the **Primary output from custom action project name** we just added to **Custom Actions** in step 4 and select **Properties Windows** to open **Properties** windows

![](/assets/img/posts/2019-12-14_12-09-10.png)

In the **Properties** windows, fill the **CustomActionData** with values as below

![](/assets/img/posts/2019-12-14_12-17-17.png)

- The **[TARGETDIR]\\** is a default parameter of VS Installer, it will pass the application directory that user selects in **Installation Folder** dialog during install process.
- The **[EDITA1]** is the value of the **Edit1Property** input in custom dialog created in step 2.
- The keys **targetdir** and **connectionstring** will be passed to Context.Parameters so we can retrieve them in codes in next step.

### Retrieve user input in code

Update the code in Install function with below to get the custom data

![](/assets/img/posts/2019-12-14_12-21-59.png)

Now we can update the connection string to app.config with the user filled value during install process.

![](/assets/img/posts/2019-12-14_12-29-17.png)

The sample codes could be found here [Console.App.Installer](https://github.com/nhvu1988-blogs-samples/Console.App.Installer).
