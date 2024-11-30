ARG NGINX_VERSION=alpine
FROM nginx:${NGINX_VERSION}

ARG PHP_UPSTREAM_PORT=${PHP_UPSTREAM_PORT}
ARG PHP_UPSTREAM_CONTAINER=${PHP_UPSTREAM_CONTAINER}

# ensure www-data user exists
RUN adduser -u 82 -D -S -G www-data www-data

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

WORKDIR /var/www
