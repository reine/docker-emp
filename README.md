# Docker Base for PHP Microservices

## Table of Contents

  - [Features](#features)
  - [Initial Setup](#initial-setup)
  - [Persisting Data](#persisting-data)
  - [Local Access](#local-access)
  - [Composer](#composer)
  - [Nginx Server Blocks](#nginx-server-blocks)
  - [Service Discovery](#service-discovery)

## Features

This docker base is currently composed of:

  - nginx
  - MariaDB 10.4.x
  - PHP 7.3.x
  - Composer
  - Consul 1.4.x

### PHP Extensions

Included in the base image are these extensions:

  - APCu
  - cURL
  - JSON
  - MCrypt (sodium in PHP>=7.1)
  - MBString
  - OPCache
  - Readline
  - XML
  - Zip

And, these are the installed additions:

  - GD
  - IMAP
  - ImageMagick
  - Intl
  - MySQL

If you need a specific extension enabled, modify `conf/php-fpm/Dockerfile`.

## Initial Setup

Clone this repository to use as your docker base for PHP-enabled sites. Launch docker and create a network:

```
docker network create emp-base
```

Update the database environment variables inside `conf/mariadb/db.env` to suit your needs.

Start and run the containers:

```
docker-compose up -d
```

## Persisting Data

To keep files down to a minimum and to prepare for a combined CI/CD (continuous integration and continuous delivery) practice, containers are removed when you stop the dockerized app. Simply meant, all your data and configurations will be lost and the next time you run the images, the database will be reinitialized.

To avoid this loss of data, volumes are mounted that will persist even after the containers were removed.

```
docker-emp
|- conf
|- data
|   |- apps
|   |- autodiscovery
|   |- db
|   \- logs
\-docker-compose.yml
```

## Local Access

Access the site via http://localhost/ and you should see the default page with the PHP version displayed at the bottom.

For most users, `localhost` will suffice. But developers who want to simulate use of domains even on a local environment would prefer to use localdomains like `local` or `test`.

Alternatively, you can access the site using http://myapps.local. Or, via http://www.myapps.local as server aliasing is acceptable so you can do subdomains.

![Main Page](/conf/screenshot.png "Main Page")

To access the database, use an SQL client and enter the following info:

```
Hostname: 127.0.0.1
Port: 8890
Username: root
Password: root123!
```

If you changed the default values in the `conf/mariadb/db.env` file, use those values instead.

Do not use the default SQL port (3306) - it will not work when accessing the database container. Use the assigned port in the `docker-compose.yml` file. The port number can be changed as long as you know what you are doing but for the purpose of this docker base, let's keep it as it is.

For security reasons, it is advisable to use SSH tunneling to access the database server for production use.

## Composer

Most PHP projects uses [Composer](https://getcomposer.org/), a dependency manager, to manage all packages required to run it. In this regard, Composer was added and have its own container that automatically exits after build.

To use, launch a terminal and navigate to the `apps` directory. Initially, check for its version to see if Composer have been properly installed.

```
$ docker-compose run --rm composer -V
Composer version 1.8.4 2019-02-11 10:52:10
```

Now, you can start using it to create your projects.

Example:

```
docker-compose run --rm composer create-project --prefer-dist yiisoft/yii2-app-basic account-api
```

A `vendor` directory will be generated and required packages will be installed. Composer-related files will be added as well.

```
docker-emp
|- conf
|- data
|   |- apps
|   |   \- account-api
|   |       |- composer.json (added by Composer)
|   |       |- composer.lock (added by Composer)
|   |       \- vendor (generated by Composer)
|   |- db
|   \- logs
\-docker-compose.yml
```

To use Composer in different apps, stop its container and edit `docker-compose.yml` with the correct persistent data path then restart it.

For example:
- To use inside /data/apps:

```
volumes:
      - ./data/apps:/var/www/html
```

- To use inside /data/apps/account-api:

```
volumes:
      - ./data/apps/account-api:/var/www/html
```

## Nginx Server Blocks

Server blocks, aka "virtual hosts", allows a single web server setup to host multiple domains.

In our example above, we can separate the new app from the main app by giving it its own domain. Check out `conf/nginx/default.conf` to see how  `account.api.local` was added to the default Nginx configuration.

This is a simplified solution to allow virtual hosting out-of-the-box using `docker-compose`. If you want a more robust solution, make sure you learn more about Docker and Nginx.

## Service Discovery

Microservice development will benefit from service discovery, specially one with health checking feature like [Consul](https://www.consul.io). It can be accessed via http://localhost:8500

![Consul](/conf/screenshot-consul.png "Consul")

---

**Work in progress.**