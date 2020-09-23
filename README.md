# Docker Symfony (PHP7-FPM - NGINX - MySQL - ELK)

[![Build Status](https://travis-ci.org/maxpou/docker-symfony.svg?branch=master)](https://travis-ci.org/maxpou/docker-symfony)

![](doc/schema.png)

Docker-symfony gives you everything you need for developing Symfony application. This complete stack run with docker and [docker-compose (1.7 or higher)](https://docs.docker.com/compose/).

## Installation

1. Create a `.env` from the `.env.dist` file. Adapt it according to your symfony application

    ```bash
    cp .env.dist .env
    ```

2. Build/run containers with (with and without detached mode)

    ```bash
    $ docker-compose build
    $ docker-compose up -d
    ```

3. Prepare Symfony app
    1. Update .env file

        From
        ```
        DATABASE_URL=sqlite:///%kernel.project_dir%/var/data/blog.sqlite
        ```
       
       To
       ```
       DATABASE_URL=mysql://username:password@127.0.0.1:3306/databasename
       ```

    2. Composer install & create database

        ```bash
        $ docker-compose exec php bash
        $ php bin/console doctrine:schema:create
        # php bin/console doctrine:fixtures:load
        ```

## Usage

Just run `docker-compose up -d`, then:

* Symfony app: visit [symfony.local](http://hello-symfony.127.0.0.1.nip.io/)  
* Symfony dev mode: visit [symfony.local/app_dev.php](http://symfony.local/app_dev.php)  
* Logs (Kibana): [symfony.local:81](http://symfony.local:81)
* Logs (files location): logs/nginx and logs/symfony

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `db`: This is the MySQL database container,
* `php`: This is the PHP-FPM container in which the application volume is mounted,
* `nginx`: This is the Nginx webserver container in which application volume is mounted too,

## Useful commands

```bash
# bash commands
$ docker-compose exec php bash

# Composer (e.g. composer update)
$ docker-compose exec php composer update

# SF commands (Tips: there is an alias inside php container)
$ docker-compose exec php php /var/www/symfony/bin/console cache:clear
# Same command by using alias
$ docker-compose exec php bash
$ sf cache:clear

# Retrieve an IP Address (here for the nginx container)
$ docker inspect --format '{{ .NetworkSettings.Networks.dockersymfony_default.IPAddress }}' $(docker ps -f name=nginx -q)
$ docker inspect $(docker ps -f name=nginx -q) | grep IPAddress

# MySQL commands
$ docker-compose exec db mysql -uroot -p"root"

# Check CPU consumption
$ docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Delete all containers
$ docker rm $(docker ps -aq)

# Delete all images
$ docker rmi $(docker images -q)
```

## FAQ

* Got this error: `ERROR: Couldn't connect to Docker daemon at http+docker://localunixsocket - is it running?
If it's at a non-standard location, specify the URL with the DOCKER_HOST environment variable.` ?  
Run `docker-compose up -d` instead.

* Permission problem? See [this doc (Setting up Permission)](http://symfony.com/doc/current/book/installation.html#checking-symfony-application-configuration-and-setup)

* How to config Xdebug?
Xdebug is configured out of the box!
Just config your IDE to connect port  `9001` and id key `PHPSTORM`

## Contributing

First of all, **thank you** for contributing ♥  
If you find any typo/misconfiguration/... please send me a PR or open an issue. You can also ping me on [twitter](https://twitter.com/_maxpou).  
Also, while creating your Pull Request on GitHub, please write a description which gives the context and/or explains why you are creating it.
