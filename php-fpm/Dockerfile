ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

# Set Timezone
ARG TZ=${TZ}
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone

COPY ./php.ini /usr/local/etc/php/php.ini
COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    apt-utils nano libz-dev libpq-dev libpng-dev \
    git libjpeg-dev libmcrypt-dev libfreetype6-dev \
    libzip-dev libicu-dev jpegoptim optipng pngquant gifsicle \
    libmagickwand-dev imagemagick libxml2-dev libxslt1-dev \
    libc-client-dev libkrb5-dev

RUN pecl channel-update pecl.php.net \
    && pecl install -o -f redis \
    && docker-php-ext-configure intl \
    && docker-php-ext-configure gd --with-jpeg --with-freetype \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install zip gd pdo_mysql calendar imap pcntl \
    exif opcache bcmath mysqli intl sockets gettext soap xsl \
    && docker-php-ext-enable redis


# Start Workaround: Get Imagick Extension
RUN git clone https://github.com/Imagick/imagick \
    && cd imagick \
    && phpize && ./configure \
    && make \
    && make install

# Move the imagick extension
RUN cd imagick \
    && EXTENSION_DIR="$( php -i | grep ^extension_dir | awk -F '=>' '{print $2}' | xargs )" \
    && if [ ! -d "${EXTENSION_DIR}" ]; then mkdir -p "${EXTENSION_DIR}"; fi \
    && cp ./modules/imagick.so "${EXTENSION_DIR}/imagick.so" \
    && cd ../ \
    && rm -rf imagick

# Enable the module
RUN docker-php-ext-enable imagick
# End Workaround: Get Imagick Extension


# Clean up
RUN apt-get clean && rm -rf /tmp/pear && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

WORKDIR /var/www
