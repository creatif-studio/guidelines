# Registry

registry is a storage and distribution system for named Docker images.

You can setup with 2 methods:

- Docker Compose: We can deploy with docker compose
- Kubernetes: We can deploy registry with rancher

## Pre-requisite

- Install `docker` and `docker-compose`
- Install `apache2-utils` and `libnss3-tools`
- Install `homebrew` and `mkcert`

## How generate

- Please change `registry.domain.com` to real domain
- Install pre-requisite
- Clone this repository `git clone git@github.com:sgnd/terry.git`
- Go to this directory `terry/developeroperations/resources/registry/auth`
- Generate password `htpasswd -b -c registry.passwd username password`
- Go to this directory `terry/developeroperations/resources/registry/nginx/ssl`
- Generate ssl `mkcert -key-file key.pem -cert-file cert.pem registry.domain.com`
- Add new line to set insecure `/etc/docker/daemon.json` in another server

```bash
{
  "insecure-registries" : ["registry.domain.com"]
}
```

- Reload docker service

### Docker Compose

- Create a new virtual machine
- Go to this directory `terry/developeroperations/resources/registry`

```bash
docker-compose up -d
```

### Kubernetes

- Make sure login to rancher
- Choose the cluster
- On the left open `workloads -> deployments`
- Create deployment
- Choose namespace `default`
- Insert name `registry`
- Choose tab `pod -> storage`
  - Choose `add volume -> bind-mount` for registry data
    - volume name: `registry-data`
    - path on node: `/mnt/registry-data`
  - Choose `add volume -> bind-mount` for registry data auth
    - volume name: `registry-data-auth`
    - path on node: `/mnt/registry-data-auth`
- Choose tab `container-0 -> general`
  - Insert container name `registry`
  - Insert image `registry:2`
  - Add ports for registry
    - service type: `node port`
    - name: `registry-port`
    - private service port: `5000`
  - Add environment
    - Type: `key/value pair`
    - Variable Name: Value
      `REGISTRY_AUTH: htpasswd`
      `REGISTRY_AUTH_HTPASSWD_REALM: Registry-Realm`
      `REGISTRY_AUTH_HTPASSWD_PATH: /auth/registry.passwd`
      `REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data`
- Choose tab `container-0 -> storage`
  - Choose `select volume -> registry-data` for registry data
    - mount point: `/data`
  - Choose `select volume -> registry-data-auth` for registry data auth
    - mount point: `/auth`
- Click button `create`
- Copy `terry/developeroperations/resources/registry/auth/registry.passwd` to server `/mnt/registry-data-auth`
