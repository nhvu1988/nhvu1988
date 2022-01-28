---
title: How to use Cloudflare Access to protect Azure Web App
date: 2022-01-28 18:00:00 +0700
author: nhvu1988
category: DevOps
tags: [azure, cloudflare]
---

While deploying my application to Azure Web App, I would like to use the [Cloudflare Access](https://www.cloudflare.com/teams/access/) to protect my web application. So that only me can use the web application.

But the problem is the Azure Web App always assign a custom domain to the web app so it could be scanned and accessed in public if anyone knows that domain. How we can prevent that?

    It could be resolved if we can restrict the access to the app from everywhere but the Cloudflare Access.

There're 4 steps we need to be done.

1. Configure the Cloudflare Access to deny user.
2. Add custom domain for Azure Web App.
3. Enable the proxy mode for the DNS on cloudflare.
4. Restrict all the traffic to the Azure Web App to be allowed from Cloudflare only.

## 1. Configure the Cloudflare Access to deny user

Go to [Cloudflare Access](https://dash.teams.cloudflare.com/) and add an application with policy to deny all users (except mine :D)

![](/assets/img/posts/2022-01-28-18-17-46.png)

## 2. Add custom domain for Azure Web App

Ensure that the custom domain has been added and configured on Azure Web App...

![](/assets/img/posts/2022-01-28-17-19-50.png)

## 3. Enable proxy mode for Cloudflare DNS

...and Cloudflare as well, but remember to **enable proxy mode** for it. So now all the traffic to Azure Web App will be proxied to Cloudflare.

![](/assets/img/posts/2022-01-28-17-24-02.png)

But because the Azure automatically assign to Web App a custom domain likes `webapp-name.azurewebservices.net` so the user may know it and can access to your application directly using that domain. So go to step 3 to prevent that.

## 4. Restrict all the traffic to the Azure Web App

Go to Networking - Access restriction

![](/assets/img/posts/2022-01-28-17-40-07.png)

Add the [Cloudflare Outbound IP Ranges](https://www.cloudflare.com/ips-v4) to the rules.

![](/assets/img/posts/2022-01-28-17-46-05.png)

But the issue is Azure Portal does not allow to add more than one IP range so you will see this error when adding it.

![](/assets/img/posts/2022-01-28-17-47-57.png)

So you need to run the powershell scripts to add there IP Ranges directly on the Azure Portal Cloud Shell. Click on the Cloud Shell icon on the top-right of portal, change the command promt to PowerShell

![](/assets/img/posts/2022-01-28-17-55-21.png)

And run the following commands

```powershell
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "resource-group-name" -WebAppName "web-app-name" -Name "Cloudflare Part 1" -IpAddress "173.245.48.0/20,103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,141.101.64.0/18,108.162.192.0/18,190.93.240.0/20,188.114.96.0/20" -Priority 100 -Action Allow

Add-AzWebAppAccessRestrictionRule -ResourceGroupName "resource-group-name" -WebAppName "web-app-name" -Name "Cloudflare Part 2" -IpAddress "197.234.240.0/22,198.41.128.0/17,162.158.0.0/15,104.16.0.0/13,104.24.0.0/14,172.64.0.0/13,131.0.72.0/22" -Priority 100 -Action Allow
```

We need to slit them to 2 rules because of this error.

```console
Add-AzWebAppAccessRestrictionRule: Cannot validate argument on parameter 'IpAddress'. Only 8 ip addresses are allowed per rule
```
