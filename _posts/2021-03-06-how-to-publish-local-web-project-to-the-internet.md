---
title: How to publish local web project to the internet
date: 2021-03-06 07:00:00 +0700
author: nhvu1988
category: DevOps
tags: [docker,cloudflared]
---

While developing project locally, sometimes I would like to publish them for client testing or having quickly demonstration. The local project is running on port `8080`

> http://localhost:8080

Thanks to [`cloudflared`](https://github.com/cloudflare/cloudflared) that we can use to publish it to the internet easily with a simple single command.

## Using binary `cloudflared`

We have to download the binary [`cloudflared`](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation) and execute this command

```bash
cloudflared tunnel --no-autoupdate --url http://localhost:1337
```

## Using docker image `cloudflare/cloudflared`

In case I want to publish a web project running on a container names `mycontainer`

Check which's network & IP address `mycontainer` is running on

```docker
docker container inspect mycontainer
```

Run the following command to publish it

```docker
docker run --rm --network=network_name cloudflare/cloudflared tunnel --no-autoupdate --url http://container_ip:8080
```