---
sort: 3
---

# Podman Installation
> (Tested on CentOS 8 and Fedora 33)

```warning
This is still experimental
```

This is continuation from [basic setup](./) 

### Install podman and podman-compose

```bash
sudo dnf install podman podman-compose
```

Log out and Log back in so that your group membership is re-evaluated. 
More info: [Docker Docs](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

You also may stop existing postgresql instance and certifire instance if present because,
it will confilict with the postgres and certifire docker instance

```bash
sudo systemctl disable --now postgresql
sudo systemctl disable --now certifire
```

### Build the Pod

```bash
git clone https://github.com/certifire/certifire
cd certifire
podman-compose build
```

### Run the Pod

```bash
podman-compose up -d
```

### Initialize

Initialize database and admin accounts (change the password as you require):

```bash
podman exec -it certifire_server_1 certifire-manager init changeme
```

If you want to stream the logs (You can press ctrl+c to quit streaming):

```bash
podman-compose logs -tf server
```

Now continue with [common setup](./#edit-nginx-configration-for-proxy)
