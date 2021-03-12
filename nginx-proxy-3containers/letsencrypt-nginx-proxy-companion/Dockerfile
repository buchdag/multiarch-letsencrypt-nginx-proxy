FROM golang:1.13-alpine AS build-docker-gen

ARG DOCKER_GEN_VERSION=0.7.4

LABEL stage=intermediate

# Install build dependencies and build docker-gen
RUN apk add --no-cache --virtual .build-deps \
        curl \
        gcc \
        git \
        make \
        musl-dev \
    && go get github.com/jwilder/docker-gen \
    && cd /go/src/github.com/jwilder/docker-gen \
    && git -c advice.detachedHead=false checkout $DOCKER_GEN_VERSION \
    && make get-deps \
    && make all \
    && go clean -cache \
    && mv docker-gen /usr/local/bin/ \
    && rm -rf /go/src \
    && apk del .build-deps

FROM alpine:3.12

LABEL maintainer="Nicolas Duchon <nicolas.duchon@gmail.com>"

ARG GIT_DESCRIBE
ARG ACMESH_VERSION=2.8.8

ENV COMPANION_VERSION=$GIT_DESCRIBE \
    DOCKER_HOST=unix:///var/run/docker.sock \
    PATH=$PATH:/app

# Copy docker-gen binary from build stage
COPY --from=build-docker-gen /usr/local/bin/docker-gen /usr/local/bin/

# Install packages required by the image
RUN apk add --no-cache --virtual .bin-deps \
        bash \
        coreutils \
        curl \
        jq \
        openssl \
        socat

# Install acme.sh and the letsencrypt service
RUN mkdir /src \
    && curl -sSL https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion/archive/master.tar.gz \
    | tar -C /src -xz \
    && mv /src/docker-letsencrypt-nginx-proxy-companion-master/app /app \
    && /src/docker-letsencrypt-nginx-proxy-companion-master/install_acme.sh \
    && rm -rf /src

WORKDIR /app

ENTRYPOINT [ "/bin/bash", "/app/entrypoint.sh" ]
CMD [ "/bin/bash", "/app/start.sh" ]
