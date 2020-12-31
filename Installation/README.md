---
sort: 1
---

# Installation

## Install dependencies: 

### (Debian/Ubuntu and derivatives)

```bash
sudo apt update && sudo apt upgrade
sudo apt install git nginx awscli certbot python3-certbot-nginx
```

### (RHEL, CentOS, Fedora)

```bash
sudo dnf upgrade
sudo dnf install git nginx awscli certbot python3-certbot-nginx
```

Configure aws credentials for route53 dns:

```bash
aws configure
```

## Select one of the following methods

{% include list.liquid all=true %}

## Edit nginx configration for proxy 

(Ubuntu based conf shown, change nginx conf path appropriately for rhel based systems)

```bash
sudo nano /etc/nginx/sites-available/default
```

Edit the config to look like this (change server_name accordingly):

    server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        server_name api.certifire.xyz;

        location / {
                try_files $uri $uri/ =404;
        }

        location /api {
                proxy_pass  http://127.0.0.1:5000;
                proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
                proxy_redirect off;
                proxy_buffering off;
                proxy_set_header        Host            $host;
                proxy_set_header        X-Real-IP       $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }

Then restart nginx

```bash
sudo systemctl restart nginx.service 
```

## (optional) Run certbot for https

```bash
sudo certbot --nginx
```

```warning
### Note

If you are hosting in an AWS EC2 instance, please make sure to edit the security groups,
Network Access Control Lists to allow traffic for ports 80 and 443 and other shenanigans.

More Info: [AWS Knowledge Center](https://aws.amazon.com/premiumsupport/knowledge-center/connect-http-https-ec2/)

There are similar quirks if hosted in GCP, ORACLE cloud etc.. 
These works out of the box for hosting services like DigitalOcean, Linode etc...

Then in the system, if you are using a firewall (defaultly enabled in rhel based systems), 
you may need to open ports 80 and 433 
```