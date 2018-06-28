ARG PHP_VERSION=7.2-fpm
FROM php:${PHP_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    apt-utils nano libz-dev libpq-dev libpng-dev \
    libjpeg-dev libmcrypt-dev libfreetype6-dev \
    libzip-dev libicu-dev jpegoptim optipng pngquant gifsicle \
    libmagickwand-dev imagemagick

RUN pecl channel-update pecl.php.net \
    && pecl install -o -f redis imagick \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-configure intl \
    && docker-php-ext-configure gd \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 \
    && docker-php-ext-install zip gd pdo_mysql \
    exif opcache bcmath pcntl mysqli intl \
    && docker-php-ext-enable redis imagick

COPY ./php.ini /usr/local/etc/php/php.ini/php.ini
COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf

USER root

# Clean up
RUN apt-get clean && rm -rf /tmp/pear && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

WORKDIR /var/www
