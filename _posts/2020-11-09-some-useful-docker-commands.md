---
title: Some useful docker commands
date: 2020-07-26 07:00:00 +0700
author: nhvu1988
category: DevOps
tags: [docker]
---

Since docker is ready installed on the machine, when I want to run/build something, I just try to find a docker image and use it right away. So I collect some useful commands for my works, hope it's also useful for yours.

The `docker run` command will create and run the container but I just want to run it once and remove it after run so I put the option `--rm` to the command. The container will be removed when it exits or when the daemon exits, whichever happens first.

The `-it` option is the combination of `-i` & `-t` for interactive process, so the command should have a same below pattern.

```docker
docker run -it --rm image:tag [command]
```

## Node

- To run a `node` command, we can use `node` image with the command after that. 
- We have to mount the current folder (using environment variable `$PWD`) to the working dir as well as define the working dir that `node` will start on.
- For example to run a single Node.js script

```docker
docker run -it --rm -v "$PWD":/app -w /app node node single-script.js
```

> `$PWD` is a linux environment variable defines current folder that the command is running on.

- To specify running node version is `8`.

```docker
docker run -it --rm -v "$PWD":/app -w /app node:8 node single-script.js
```

- Or to run `npm install`

```docker
docker run -it --rm -v "$PWD":/app -w /app node npm install
```

## Angular CLI

- To run a `ng` command, we can use `trion/ng-cli` image.
- Since the image automatically sets the working dir to `/app` so we don't need to define it but mounting the volumn as project folder to `/app`.
- For example to run the create new angular application.

```docker
docker run -it --rm -v "$PWD":/app trion/ng-cli ng new website
```
- And to generate a component

```docker
docker run -it --rm -v "$PWD":/app trion/ng-cli ng g c new-component
```

## MongoDB

- To run a `mongo` command, we can use [mongo](https://hub.docker.com/_/mongo) image.

### Mongo 3.6

- To generate a database backup with mongodump

```docker
docker run -it --rm -v $PWD:/backup mongo:3.6 sh -c "mongodump --uri=mongodb://username:password@mongo_uri:27017/dbname --authenticationDatabase=admin --archive='/backup/dbname.archive'"
```

### Mongo 4.2

- To generate a database backup with mongodump

```docker
docker run -it --rm -v $PWD:/backup mongo sh -c "mongodump --uri=mongodb://username:password@mongo_uri:27017/dbname --authenticationDatabase=admin --archive='/backup/dbname.archive'"
```

- If the mongo server instance is running on localhost using docker-compose, get it's network name with command

```docker
docker network ls
```

- And use the network name in `--net` parameter

```docker
docker run -it --rm -v $PWD:/backup --net network_name mongo sh -c "mongodump --uri=mongodb://username:password@mongo_container_name:27017/dbname --authenticationDatabase=admin --archive='/backup/dbname.archive'"
```

- To restore that database backup file

```docker
docker run -it --rm -v $PWD:/backup mongo sh -c "mongorestore --uri=mongodb://username:password@mongouri:27017/dbname --authenticationDatabase=admin --archive='/backup/dbname.archive'"
```

