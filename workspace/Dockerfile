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
COPY ./xdebug.ini /usr/local/etc/php/xdebug.ini
COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./.bashrc /root/.bashrc

RUN apt-get update \
    && apt-get install -y --no-install-recommends locales\
    git git-flow zip unzip curl ntp whois dnsutils \
    libmcrypt4 libpcre3-dev bash-completion locate \
    apt-utils nano libz-dev libpq-dev libpng-dev \
    libjpeg-dev libmcrypt-dev libfreetype6-dev \
    libzip-dev libicu-dev jpegoptim optipng pngquant gifsicle \
    python3 python3-pip python3-dev python3-setuptools build-essential \
    libmagickwand-dev imagemagick libxml2-dev libxslt1-dev \
    libc-client-dev libkrb5-dev openssh-client gnupg \
    && python3 -m pip install --upgrade pip \
    && python3 -m pip install --upgrade virtualenv

# Set Locale (after installing locales)
RUN echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
RUN locale-gen en_US.UTF-8

RUN pecl channel-update pecl.php.net \
    && pecl install -o -f redis xdebug \
    && docker-php-ext-configure intl \
    && docker-php-ext-configure gd --with-jpeg --with-freetype \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install zip gd pdo_mysql calendar imap pcntl \
    exif opcache bcmath mysqli intl sockets gettext soap xsl \
    && docker-php-ext-enable redis xdebug


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


# Install Composer
ENV COMPOSER_DIR /root/.composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && printf "\nPATH=\"$COMPOSER_DIR/vendor/bin:\$PATH\"\n" | tee -a /root/.bashrc


# Node / NVM
ARG NODE_VERSION=stable
ENV NODE_VERSION ${NODE_VERSION}
ENV NVM_DIR /root/.nvm

RUN mkdir -p ${NVM_DIR} \
    && curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.38.0/install.sh | bash \
    && . ${NVM_DIR}/nvm.sh \
    && nvm install ${NODE_VERSION} \
    && nvm use ${NODE_VERSION} \
    && nvm alias ${NODE_VERSION}

# Wouldn't execute when added to the RUN statement in the above block
# Source NVM when loading bash since /root/.profile isn't loaded on non-login shell
RUN echo "" >> /root/.bashrc \
    && echo 'export NVM_DIR="${NVM_DIR}/.nvm"' >> /root/.bashrc \
    && echo '[ -s "${NVM_DIR}/nvm.sh" ] && . "${NVM_DIR}/nvm.sh"  # This loads nvm' >> /root/.bashrc \
    && printf "\nPATH=\"${NVM_DIR}/vendor/bin:\$PATH\"\n" | tee -a /root/.bashrc \
    && exec bash && . /root/.bashrc && npm install -g svgo

# Clean up
RUN apt-get clean && rm -rf /tmp/pear && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

WORKDIR /var/www
