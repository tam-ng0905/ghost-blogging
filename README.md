# ghost-blogging
# Docker Compose Quickstart for Ghost Blogging Platform

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/robincher/docker-compose-ghost-quickstart/blob/master/LICENSE)

Quick start docker compose that include Ghost blog, Nginx proxy with TLS/SSL and MySQL database. The intention is to get a general feel of depolying Ghost in production mode, where each component (Web , App & DB) are deployed seperately. This supports scaling and isolate failure (if any).

The compose file will pull the latest tag of each respecitve docker images before running them. Additionally, it will also persist data generated by the containters using Docker's volume.

If you want to test out Ghost locally and just to explore its feature, you should consider installing ghost-cli instead.

```
npm install -g ghost-cli@latest # Install Ghost 
```

```
ghost install local # Running on Dev
```


## Table of Contents
- [System Overview](#overview)
- [Opearting Env](#opearting-env)
- [Technolgoy Stack](#technology-stack)
- [Preparation](#technology-stack)
- [How to use it](#how-to-use-it)
- [Database Backup](#how-to-back-up-your-database)
- [Install Customised Theme](#install-theme)
- [Security and Networking](#security-and-networking)
- [License](#license)
- [References](#references)

## Overview

![ghost.png](https://github.com/robincher/docker-compose-ghost-quickstart/blob/master/assets/ghost-compose-context.png)

## Operating ENV
- **Operating System** Redhat Enterprise Linux 5.7

- **docker** version 1.12.2

- **docker-compose** version 1.9.0

## Technology Stack
- Node.js
    - Ghost blog software version 1.x
- NGINX
    - proxying port 80 calls to the Node web server on port 2368
- MySQL database
    - using UTFMB4 encoding (MySQL's UTF8 implementation was limited. UTFMB4 includes Emoji)

## Preparation
- A server sitting somewhere, like Amazon EC2 , Google Compute Cloud or a virutal machine connected to your own network
- Register a domain name (either public or within your own network).
- A pair of SSL certificate and key for your domain.
- Access to your domain's DNS.

## How to Use It
1. Clone this project into the server's filesystem.
2. Edit settings in config.<<env>>.json and docker-compose.yml as required
    - config.production.json and update the fields as required
    - Database Initial set-up settings - under ghost and mysql services in docker-compose file, indicate the env variables
3. Add your TLS/SSL certificate and key to ./nginx/ssl
4. Set your registered domain (server_name) in ./nginx/conf/ghost.conf (must match the common name in your TLS/SSL certificate)
5. Run docke-compose within your Linux environment
    0. ```docker-compose build```
    0. ```docker-compose up -d```  (you can remove the -d if you want to see logs, then Ctrl+C to stop all containers)
6. Go to https://YOUR_DOMAIN/admin to set up your blog.
7. Once you've made your admin account using that wizard, go back to: https://YOUR_DOMAIN/admin
8. Log in and enjoy.
9. Stopping and remove all containers : ```docker-compose down --remove-orphans```

## How to back up your database
1. Run "docker-compose ps" to get a list of running containers.
2. Locate the name of the mysql container.
3. Run this command to get the container's internal IP: docker inspect --format='{{.NetworkSettings.IPAddress}}' THAT_CONTAINER-NAME
4. In your favorite database GUI tool (like Navicat or DataGrip), create a new connection via SSH tunnel to the host machine
5. Use the internal IP address and database user and password to connect to database once SSH tunnel is established to host.
6. You'll have access to the data so you can view data and run backups.

## Install Theme

Stop docker-compose with:
```docker-compose stop```

Copy the new theme to **./ghost/content/theme** so that your theme folder sits next to the casper folder in the themes directory

Now run:
```docker-compose up -d```

Log in to the Ghost admin, go to Settings > General, and at the bottom is the Theme dropdown. Select your theme and click Save.

**How does that work?**

The ./ghost/content directory (on docker host machine) gets mounted inside your ghost container folder /var/lib/ghost when the container starts running. See docker-compose.yml for more details

## Security and Networking
- Only NGINX's ports (443) are exposed at host level.
- Support only TLS version 1.2 for incoming traffic
- HTTP/2 Enabled for Nginx Reverse Proxy

## License
[MIT LICENSE ](https://github.com/robincher/docker-compose-ghost-quickstart/blob/master/LICENSE)

## References
+ [Docker Compose for Ghost by John Washam](https://github.com/jwasham/docker-ghost-template)
+ [Ghost @ Dockerhub](https://hub.docker.com/_/ghost/)
+ [Installing Ghost locally for Dev](https://docs.ghost.org/docs/install-local)

