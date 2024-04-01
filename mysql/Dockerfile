ARG MYSQL_VERSION=${MYSQL_VERSION}
FROM mysql/mysql-server:${MYSQL_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

# Set Timezone
ARG TZ=${TZ}
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo ${TZ} > /etc/timezone

# Fix filesystem permissions
RUN chown -R mysql:root /var/lib/mysql/

# Copy config file
COPY my.cnf /etc/mysql/conf.d/my.cnf
