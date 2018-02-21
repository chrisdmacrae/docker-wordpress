## Docker Wordpress
A development & production-ready Dockerized Wordpress installation. ✨

## Overview
Why does this exist? Because Wordpress development sucks, and deploying Wordpress sucks. Does Wordpress suck? No? 🙊

This allows you to very quickly setup a foolproof, environment-agnostic Wordpress installation that can be mirrored in production. It handles:

- Managing your Wordpress version
- Developing plugins
- Developing themes
- Managing Wordpress & plugins with WPCLI
- Doing database backups locally, to S3, or via SMB
- Load balancing across a Docker Swarm using Traefik
- Docker management using Portainer
- Database management using PhpMyAdmin
- Securing everything using basic auth *(it's good enough 🤷‍♀️)*
- 💥

## Installation
Before you get started, you'll need [Docker](https://docs.docker.com/install/).

Then, all you need to do is cd into your project directory, and run:

```
docker-compose up
```

Ta-da! Your installation is up and running. 🙌

- [Access Wordpress](http://wordpress.docker.localhost) at `http://wordpress.docker.localhost`
- [Access Traefik Loadbalancer](http://monitor.docker.localhost) at `http://monitor.docker.localhost`
- [Access PhpMyAdmin](http://phpmyadmin.docker.localhost) at `http://phpmyadmin.docker.localhost`
- [Access Portainer Docker Manager](http://portainer.docker.localhost) at `http://portainer.docker.localhost`

## Usage
Once you've started the container, you can spin it up and down at will by running:

Stop the services:
```
docker-compose stop
```

Start the services:
```
docker-compose start
```

The project will persist data even if the services crash or are manually shut down, so feel free to save resources by running:

```
docker-compose down
```

*This will remove all containers and networks, saving space*.

## Configuration
At this point, your server is *not* configured securely. *That sucks*!

Open up `.env` and change the following:

- `LB_DOMAIN`: the domain you want to access your development environment from. Defaults to `docker.localhost`
- `LB_AUTH`: the username and password you want to restrict access to PHPMyAdmin, Traefik, and Portainer with. See [setting up auth](#setting-up-auth)
- `WORDPRESS_DB_NAME`: the name of the database in MariaDB that Wordpress will use. Defaults to `wordpress`.
- `WORDPRESS_DB_USER`: the user that Wordpress will use to connect to the database. Defaults to `wordpress`.
- `WORDPRESS_DB_PASSWORD`: the Wordpress users password. Defaults to `changeme2018`. **CHANGE THIS!!!**
- `MYSQL_ROOT_PASSWORD`: the MYSQL database's root user password. Defaults to `changeme2018`. **CHANGE THIS!!!**

### Setting Up Auth
Auth is run through Traefik using a key-value pair (user:password). The password must be encyrpted using MD5. This can be done using htpasswd:

```
htpasswd -n [username]
```

htpasswd will log the key-value pair to stdout, which you can then provide via the `.env` file via `LB_AUTH`.

### Production
- `LB_EMAIL`: the email to run LetsEncrypt SSL generation with

### Backups
Backups can be configured through the `.env` file:

- `MYSQL_DUMP_BEGIN`: when to do the first dump. Either an absolute time `HHMM` (e.g, `1200`, 12:00pm) or a positive offset in minutes `+MM` (e.g `+60` for one hour). Defaults to `+60`.
- `MYSQL_DUMP_FREQ`: how often you want to run backups in minutes.
- `MYSQL_DUMP_TARGET`: where to store database backups in the container. Defaults to `/db`
- `MYSQL_DUMP_DEBUG`: output debug information for backups. `true` or `false`.


#### Remote Backups
You can run remote backups to Amazon S3 or to a remote server via SMB.

##### Amazon S3

In the `.env` file:

- Set `MYSQL_DUMP_TARGET` to `s3://{{ YOUR_BUCKET_NAME }}/PATH`. *`PATH` can be omitted to write to the root of the bucket.*
- Provide `AWS_ACCESS_KEY_ID`
- Provide `AWS_SECRET_ACCESS_KEY`
- Provide `AWS_DEFAULT_REGION`. Defaults to `us-west-2`

##### Remote Server via SMB

In the `.env` file:

- Set `MYSQL_DUMP_TARGET` to `smb://hostname/share/path/`. *`PATH` can be omitted to write to the root of the bucket.*
- Provide `SMB_USER`
- Provide `SMB_PASS`