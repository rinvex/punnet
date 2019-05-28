ARG MYSQL_VERSION=8.0
FROM mysql:${MYSQL_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

# Install nano
RUN apt-get update && apt-get install -y --no-install-recommends nano && rm -rf /var/lib/apt/lists/*

# Set Timezone
ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
	&& echo $TZ > /etc/timezone

# Fix filesystem permissions
RUN chown -R mysql:root /var/lib/mysql/

# Copy config file
COPY my.cnf /etc/mysql/conf.d/my.cnf
