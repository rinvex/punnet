# Rinvex Punnet

**Rinvex Punnet** is opinionated, lightweight, yet powerful, and performant light speed docker environment for PHP applications.

[![Packagist](https://img.shields.io/packagist/v/rinvex/punnet.svg?label=Packagist&style=flat-square)](https://packagist.org/packages/rinvex/punnet)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/rinvex/punnet.svg?label=Scrutinizer&style=flat-square)](https://scrutinizer-ci.com/g/rinvex/punnet/)
[![Code Climate](https://img.shields.io/codeclimate/github/rinvex/punnet.svg?label=CodeClimate&style=flat-square)](https://codeclimate.com/github/rinvex/punnet)
[![Travis](https://img.shields.io/travis/rinvex/punnet.svg?label=TravisCI&style=flat-square)](https://travis-ci.org/rinvex/punnet)
[![StyleCI](https://styleci.io/repos/87485079/shield)](https://styleci.io/repos/87485079)
[![License](https://img.shields.io/packagist/l/rinvex/punnet.svg?label=License&style=flat-square)](https://github.com/rinvex/punnet/blob/develop/LICENSE)


It's a full PHP development environment based on [Docker](https://www.docker.com).

This environment includes pre-packaged Docker images, all pre-configured to provide a wonderful PHP development environment, with flexibility, and performance in mind.


<a name="features"></a>
## Features

- Easy switch between PHP versions: 7.4, 7.3, 7.2, 7.1...
- Choose your favorite database engine: MySQL
- Every software runs on a separate container: PHP-FPM, NGINX, PHP-CLI...
- Easy to customize any container, with simple edit to the `Dockerfile`.
- All Images extends from an official base Image. (Trusted base Images).
- Pre-configured NGINX to host any code at your root directory.
- Can use **Rinvex Punnet** per project, or single **Rinvex Punnet** instance for all projects.
- Easy to install/remove software in Containers using environment variables.
- Clean and well structured Dockerfiles (`Dockerfile`).
- Latest version of the Docker Compose file (`docker-compose`).
- Everything is visible and editable.
- Fast Images Builds.
- More to come!


## Getting Started

<a name="requirements"></a>
### Requirements

- [Git](https://git-scm.com/downloads)
- [Docker](https://www.docker.com/products/docker/) `>= 18.00.0`


<a name="installation"></a>
### Installation

Follow these steps if you want a one docker environment for multiple projects.

Let's see how easy it is to install **NGINX**, **PHP**, **Composer**, **MySQL**, and **Redis**, all at once:


1. Install **Rinvex Punnet** outside the project directory:

    Clone this repository anywhere on your machine:

    ```bash
    git clone https://github.com/rinvex/punnet.git
    ```

    Your folder structure should look like this:

    ```
    + punnet
    + project-a
    + project-b
    ```


2. Prepare your docker environment config:

    ```
    cp .env.example .env
    ```
    
    At the top of your docker `.env` file, change the `WEB_DATA_PATH` variable to your project path:

    ```
    WEB_DATA_PATH=../project-a/
    ```

    Make sure to replace `project-a` with your project folder name.
  
    You can edit the docker environment config `.env` file to choose which software you want to be installed in your environment. You can always refer to the `docker-compose.yml` file to see how those variables have been used.
    
    Depending on the host's operating system you may need to change the value given to `COMPOSE_FILE`. When you are running **Rinvex Punnet** on Mac OS the correct file separator to use is `:`. When running Punnet from a Windows environment multiple files must be separated with `;`.
    

3. Build and run your containers:

    ```shell
    docker-compose up -d redis mysql portainer workspace php-fpm nginx 
    ```

    **Note**: All the web server containers `nginx` depends on `php-fpm`, which means if you run any of them, they will automatically launch the `php-fpm` container for you, so no need to explicitly specify it in the `up` command. If you have to do so, you may need to run them as follows: `docker-compose up -d nginx php-fpm mysql`.
    
    *(Please note that sometimes we forget to update the docs, so check the `docker-compose.yml` file to see an updated list of all available containers).*


4. Configure your PHP project to use `mysql` and `redis` service containers.

    Open your PHP project's `.env` file or whichever configuration file you are reading from, and set the database host `DB_HOST` to `mysql`, and `REDIS_HOST` to `redis`:

    ```shell
    DB_HOST=mysql
    REDIS_HOST=redis
    ```


5. Go to your hosts file `/etc/hosts` and add your local domains:

    ```shell
    127.0.0.1  cortex.rinvex.test
    127.0.0.1  custom-project.test
    ```
   
   If you use Chrome 63 or above for development, don't use `.dev`. [Why?](https://laravel-news.com/chrome-63-now-forces-dev-domains-https). Instead use `.localhost`, `.invalid`, `.test`, or `.example`.


6. Go to `/PATH/TO/PUNNET/.config/nginx/sites/` and create config files to point to different project directory when visiting different domains.

    **Rinvex Punnet** by default includes `app.conf.example`, `laravel.conf.example`  as working samples. NGINX local domain config could be like the following example:

    ```shell
    server {
    
        listen 80;
        listen [::]:80;
    
        server_name .cortex.rinvex.test;
        root /var/www/rinvex/cortex/public;
        index index.php index.html index.htm;
    
        location / {
             try_files $uri $uri/ /index.php$is_args$args;
        }
    
        location ~ \.php$ {
            try_files $uri /index.php =404;
            fastcgi_pass php-upstream;
            fastcgi_index index.php;
            fastcgi_buffering off;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            #fixes timeouts
            fastcgi_read_timeout 600;
            include fastcgi_params;
        }
    
        location ~ /\.ht {
            deny all;
        }
    
        error_log /var/log/nginx/rinvex/cortex/error.log;
        access_log /var/log/nginx/rinvex/cortex/access.log;
    }
    ```

    You can rename the config files, project folders and local domains as you like, just make sure the `root` in the config files, is pointing to the correct project folder name.


7. To enter the Workspace container, and execute commands like (Artisan, Composer, PHPUnit, ...)

    ```bash
    docker-compose exec workspace /bin/bash
    ```
   
    Some helpful commands:
    
    ```bash
    docker-compose logs CONTAINER_NAME
    ```


8. Now you can browse your local sites in your browser, like: `http://cortex.rinvex.test`.


<a name="supported-containers"></a>
## Supported Software (Images)

In adhering to the separation of concerns principle as promoted by Docker, Punnet runs each software on its own Container.
You can turn On/Off as many instances of as any container without worrying about the configurations, everything works like a charm.

- **Database Engines:** MySQL
- **Cache Engines:** Redis
- **PHP Servers:** NGINX
- **PHP Compilers:** PHP FPM
- **Random Tools:** Portainer

Punnet introduces the **Workspace** Image, as a development environment.
It contains a rich set of helpful tools, all pre-configured to work and integrate with almost any combination of Containers and tools you may choose.

**Workspace Image Tools**
PHP CLI - Composer - Git - Node - SQLite - xDebug - Envoy - Deployer, Laravel installer, PHP codesniffer, PHP CS Fixer, and many more...

You can choose, which tools to install in your workspace container and other containers, from the `.env` file.

> If you modify `docker-compose.yml`, `.env` or any `dockerfile` file, you must re-build your containers, to see those effects in the running instance.

If you can't find your Software in the list, build it yourself and submit it. Contributions are welcomed :)


<a name="what-is-docker"></a>
## What is Docker?

[Docker](https://www.docker.com) is an open platform for developing, shipping, and running applications.
Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.
With Docker, you can manage your infrastructure in the same ways you manage your applications.
By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.


<a name="why-docker-not-vagrant"></a>
## Why Docker not Vagrant!?

[Vagrant](https://www.vagrantup.com) creates Virtual Machines in minutes while Docker creates Virtual Containers in seconds.

Instead of providing a full Virtual Machines, like you get with Vagrant, Docker provides you **lightweight** Virtual Containers, that share the same kernel and allow to safely execute independent processes.

In addition to the speed, Docker gives tons of features that cannot be achieved with Vagrant.

Most importantly Docker can run on Development and on Production (same environment everywhere). While Vagrant is designed for Development only, (so you have to re-provision your server on Production every time).


## Changelog

Refer to the [Changelog](CHANGELOG.md) for a full history of the project.


## Support

The following support channels are available at your fingertips:

- [Chat on Slack](https://bit.ly/rinvex-slack)
- [Help on Email](mailto:help@rinvex.com)
- [Follow on Twitter](https://twitter.com/rinvex)


## Contributing & Protocols

Thank you for considering contributing to this project! The contribution guide can be found in [CONTRIBUTING.md](CONTRIBUTING.md).

Bug reports, feature requests, and pull requests are very welcome.

- [Versioning](CONTRIBUTING.md#versioning)
- [Pull Requests](CONTRIBUTING.md#pull-requests)
- [Coding Standards](CONTRIBUTING.md#coding-standards)
- [Feature Requests](CONTRIBUTING.md#feature-requests)
- [Git Flow](CONTRIBUTING.md#git-flow)


## Security Vulnerabilities

If you discover a security vulnerability within this project, please send an e-mail to [help@rinvex.com](help@rinvex.com). All security vulnerabilities will be promptly addressed.


## About Rinvex

Rinvex is a software solutions startup, specialized in integrated enterprise solutions for SMEs established in Alexandria, Egypt since June 2016. We believe that our drive The Value, The Reach, and The Impact is what differentiates us and unleash the endless possibilities of our philosophy through the power of software. We like to call it Innovation At The Speed Of Life. That’s how we do our share of advancing humanity.


## License

This software is released under [The MIT License (MIT)](LICENSE).

(c) 2016-2020 Rinvex LLC, Some rights reserved.
