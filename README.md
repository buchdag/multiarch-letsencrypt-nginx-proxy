## nginx-proxy with letsencrypt-nginx-proxy-companion on armhf and arm64

This repo provide a simplified way to run up to date [nginx-proxy](https://github.com/nginx-proxy/nginx-proxy) stack with [Let's Encrypt support](https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion) on non `amd64` architectures.

It requires Docker 17.05+ and [docker-compose](https://docs.docker.com/compose/install/#install-compose) to run.

### Usage:

- `git clone https://github.com/buchdag/multiarch-letsencrypt-nginx-proxy`

Depending on [which](https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion/blob/master/docs/Basic-usage.md) [setup](https://github.com/nginx-proxy/docker-letsencrypt-nginx-proxy-companion/blob/master/docs/Advanced-usage) you want, either

- `cd multiarch-letsencrypt-nginx-proxy/nginx-proxy-2containers`

or

- `cd multiarch-letsencrypt-nginx-proxy/nginx-proxy-3containers`

- `docker-compose up -d`

Please note that on low end ARM devices, container build will take a while and DH parameters generation by `letsencrypt-nginx-proxy-companion` might take even longer.

Certificate issuance was successfully tested on [Scaleway](https://www.scaleway.com/) `C1` and `ARM64-2GB` servers.

The two multi-stage Dockerfiles will produce a build container that won't be automatically cleaned afterwards. You can remove it with `docker image prune --filter label=stage=intermediate`.
