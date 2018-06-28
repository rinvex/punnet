ARG NGINX_VERSION=1.15
FROM nginx:${NGINX_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

COPY nginx.conf /etc/nginx/

# Install nano
RUN apt-get update && apt-get install -y --no-install-recommends nano && rm -rf /var/lib/apt/lists/*

ARG PHP_UPSTREAM_PORT=9000
ARG PHP_UPSTREAM_CONTAINER=php-fpm

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf
