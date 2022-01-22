---
title: How to get saved wifi password in windows?
author: nhvu1998
date: 2016-08-05
category: DevOps
tags: [wifi,password]
---

1. Open CMD in Administration

    ![Open CMD in Administration](/assets/img/posts/how-to-get-saved-wifi-password-in-windows-1.png)

2. Run below command to see all saved wifi profiles in your PC. Pick the wifi name to see password

    ```bash
    netsh wlan show profile
    ```

    ![Run command to see saved wifi profiles](/assets/img/posts/how-to-get-saved-wifi-password-in-windows-2.png)

3. Run below command (replaces hoangvu to your wifi name) and see the saved password in `Key Content` section

    ```bash
    netsh wlan show profile hoangvu key=clear
    ```

    ![Run command to see the saved password](/assets/img/posts/how-to-get-saved-wifi-password-in-windows-3.png)
