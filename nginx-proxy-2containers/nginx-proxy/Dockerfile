FROM golang:1.10-alpine AS go-builder

ARG DOCKER_GEN_VERSION=0.7.4
ARG FOREGO_VERSION=20180216151118

LABEL stage=intermediate

# Install build dependencies for docker-gen and forego
RUN apk add --update \
	curl \
	gcc \
	git \
	make \
	musl-dev

# Build forego
RUN go get github.com/ddollar/forego \
    && cd /go/src/github.com/ddollar/forego \
    && git checkout $FOREGO_VERSION \
    && make all

# Build docker-gen
RUN go get github.com/jwilder/docker-gen \
    && cd /go/src/github.com/jwilder/docker-gen \
    && git checkout $DOCKER_GEN_VERSION \
    && make get-deps \
    && make all

FROM nginx:1.13-alpine

LABEL maintainer="Nicolas Duchon <nicolas.duchon@gmail.com>"

# DOCKER_GEN_VERSION environment variable is required by letsencrypt-nginx-proxy-companion
ENV DOCKER_GEN_VERSION=0.7.4 \
    DOCKER_HOST=unix:///tmp/docker.sock

# Install/update run dependencies
RUN apk add --update \
    bash \
    ca-certificates \
    curl \
    openssl \
    && rm -rf /var/cache/apk/*

# Configure Nginx and apply fix for very long server names
RUN echo "daemon off;" >> /etc/nginx/nginx.conf \
    && sed -i 's/worker_processes  1/worker_processes  auto/' /etc/nginx/nginx.conf

# Copy forego and docker-gen binaries from build stage
COPY --from=go-builder /go/src/github.com/ddollar/forego/forego /usr/local/bin/
COPY --from=go-builder /go/src/github.com/jwilder/docker-gen/docker-gen /usr/local/bin/

# Install nginx-proxy
RUN mkdir /src /app \
    && curl -sSL https://github.com/jwilder/nginx-proxy/archive/master.tar.gz \
    | tar -C /src -xz \
    && cp /src/nginx-proxy-master/Procfile /app/ \
    && cp /src/nginx-proxy-master/dhparam.pem.default /app/ \
    && cp /src/nginx-proxy-master/docker-entrypoint.sh /app/ \
    && cp /src/nginx-proxy-master/generate-dhparam.sh /app/ \
    && cp /src/nginx-proxy-master/nginx.tmpl /app/ \
    && cp /src/nginx-proxy-master/network_internal.conf /etc/nginx/ \
    && rm -rf /src

WORKDIR /app

VOLUME ["/etc/nginx/certs", "/etc/nginx/dhparam"]

ENTRYPOINT ["/app/docker-entrypoint.sh"]
CMD ["forego", "start", "-r"]
