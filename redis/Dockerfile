ARG REDIS_VERSION=4.0
FROM redis:${REDIS_VERSION}

LABEL maintainer="Abdelrahman Omran <me@omranic.com>"

RUN apt-get update && apt-get install -y --no-install-recommends nano && rm -rf /var/lib/apt/lists/*
