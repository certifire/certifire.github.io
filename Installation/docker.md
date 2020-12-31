---
sort: 2
---

# Docker Installation
> (Tested on Ubuntu 20.04)

This is continuation from [basic setup](./) 

### Install docker and docker-compose

```bash
sudo apt install docker docker-compose
sudo groupadd docker
sudo usermod -aG docker certifire
```

Log out and Log back in so that your group membership is re-evaluated. 
More info: [Docker Docs](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

You also may stop existing postgresql instance and certifire instance if present because,
it will confilict with the postgres and certifire docker instance

```bash
sudo systemctl disable --now postgresql
sudo systemctl disable --now certifire
```

### Build docker image

```bash
git clone https://github.com/certifire/certifire
cd certifire
docker-compose build
```

### Run the container

```bash
docker-compose up -d
```

### Initialize

Initialize database and admin accounts (change the password as you require):

```bash
docker-compose exec server certifire-manager init -p changeme
```

If you want to stream the logs (You can press ctrl+c to quit streaming):

```bash
docker-compose logs -tf server
```

Now continue with [common setup](./#edit-nginx-configration-for-proxy)
