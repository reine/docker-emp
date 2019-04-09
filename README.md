# Docker Base for NginX, MariaDB and PHP-FPM

## Initial Setup

Clone this repository to use as your docker base for PHP-enabled sites. Launch docker and create a network:

```
docker network create emp-base
```

Update the database environment variables inside **conf/mariadb/db.env** to suit your needs.

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
|   \- db
|-docker-compose.yml
```

**Work in progress.**