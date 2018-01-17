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

ENV DOCKER_HOST=unix:///tmp/docker.sock

# Get latest nginx.tmpl
ADD https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl /etc/docker-gen/templates/

ENTRYPOINT ["/usr/local/bin/docker-gen"]
