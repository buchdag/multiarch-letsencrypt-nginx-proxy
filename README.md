## nginx-proxy with letsencrypt-nginx-proxy-companion on armhf and arm64

### [THIS IS CURRENTLY NOT WORKING ON ARMHF, DO NOT USE IT ON THIS ARCH.](https://github.com/buchdag/multiarch-letsencrypt-nginx-proxy/issues/1)

This repo provide a simplified way to run up to date [nginx-proxy](https://github.com/jwilder/nginx-proxy) stack with [Let's Encrypt support](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) on non `amd64` architectures.

It requires [docker-compose](https://docs.docker.com/compose/install/#install-compose) to run.

### Usage:

1. `git clone https://github.com/buchdag/multiarch-letsencrypt-nginx-proxy`

2. `cd multiarch-letsencrypt-nginx-proxy`

3. Edit `docker-compose.yaml` and change the two default `ARCH: amd64` build arguments to the architecture you're running on (either `ARCH: armhf` or `ARCH: arm64`).

4. `docker-compose up -d`

Certificate issuance was sucessfully tested on [Scaleway](https://www.scaleway.com/) `C1` and `ARM64-2GB` servers.

**Note:** the docker-gen binaries are downloaded from the `buchdag/docker-gen` fork because upstream `jwilder/docker-gen` does not provide `arm64` binaries yet. A [pull request](https://github.com/jwilder/docker-gen/pull/272) has been opened to add those binaries to upstream releases.
