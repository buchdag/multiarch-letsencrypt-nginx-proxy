FROM golang:1.10-alpine AS build-docker-gen

ARG DOCKER_GEN_VERSION=0.7.4

LABEL stage=intermediate

# Install build dependencies for docker-gen
RUN apk add --update \
	curl \
	gcc \
	git \
	make \
	musl-dev

# Build docker-gen
RUN go get github.com/jwilder/docker-gen \
    && cd /go/src/github.com/jwilder/docker-gen \
    && git checkout $DOCKER_GEN_VERSION \
    && make get-deps \
    && make all

FROM alpine:3.8

LABEL maintainer="Nicolas Duchon <nicolas.duchon@gmail.com>"

ENV DEBUG=false \
    DOCKER_HOST=unix:///var/run/docker.sock

# Install curl
RUN apk add --update curl \
    && rm -rf /var/cache/apk/*

# Copy docker-gen binary from build stage
COPY --from=build-docker-gen /go/src/github.com/jwilder/docker-gen/docker-gen /usr/local/bin/

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
