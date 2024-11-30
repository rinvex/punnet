ARG MARIADB_VERSION=latest
FROM mariadb:${MARIADB_VERSION}

# Set Timezone
ARG TZ=${TZ}
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo ${TZ} > /etc/timezone

# Fix filesystem permissions
RUN chown -R mysql:root /var/lib/mysql/

# Copy config file
COPY ./my.cnf /etc/mysql/conf.d/my.cnf
