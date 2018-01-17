FROM alpine:3.7

LABEL maintainer="Nicolas Duchon <nicolas.duchon@gmail.com>"

ARG DOCKER_GEN_REPO=buchdag/docker-gen
ARG DOCKER_GEN_VERSION=0.7.4
ARG OS=alpine-linux
ARG ARCH=amd64

# Install curl
RUN apk add --update curl \
    && rm -rf /var/cache/apk/*

# Install docker-gen binary
RUN curl -sSL https://github.com/$DOCKER_GEN_REPO/releases/download/$DOCKER_GEN_VERSION/docker-gen-$OS-$ARCH-$DOCKER_GEN_VERSION.tar.gz \
    | tar -C /usr/local/bin -xz

ENV DOCKER_HOST=unix:///var/run/docker.sock \
    DEBUG=false

# Install packages required by simp_le
RUN apk add --update \
        bash \
        jq \
        openssl \
    && rm /var/cache/apk/*

# Install simp_le and the letsencrypt service
RUN mkdir /src \
    && curl -sSL https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion/archive/master.tar.gz \
    | tar -C /src -xz \
    && mv /src/docker-letsencrypt-nginx-proxy-companion-master/app /app \
    && /src/docker-letsencrypt-nginx-proxy-companion-master/install_simp_le.sh \
    && rm -rf /src

WORKDIR /app

ENTRYPOINT [ "/bin/bash", "/app/entrypoint.sh" ]
CMD [ "/bin/bash", "/app/start.sh" ]
