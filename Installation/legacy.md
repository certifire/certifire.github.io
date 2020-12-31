---
sort: 1
---

# Legacy Installation
> (Tested on Ubuntu 20.04)

This is continuation from [basic setup](./) 

### Create and switch to new user

```bash
sudo useradd -m -s /bin/bash -G sudo -c "Certifire API Server" certifire
sudo passwd certifire
sudo su - certifire
```

### Install dependencies

```bash
sudo apt install python3-dev python3-pip python3-virtualenv libpq-dev build-essential libssl-dev libffi-dev postgresql
sudo systemctl enable --now postgresql.service
```

### Setup Postgresql

```bash
    $ sudo su - postgres
    $ psql
```
```sql
    postgres# CREATE USER certifire WITH PASSWORD 'certifire';
    postgres# ALTER USER certifire WITH SUPERUSER;
    postgres# CREATE DATABASE certifire;
    postgres# exit
```
```bash
exit
```

### Setup Certifire

After cloning, create a virtual environment and install the requirements. For Linux and Mac users:

```bash
    $ git clone https://github.com/certifire/certifire
    $ virtualenv -p python3 certifire
    $ source certifire/bin/activate
    $ cd certifire
    (certifire) $ pip install -r requirements.txt
```

Make appropriate changes to [config.py](./certifire/config.py)

```bash
    (certifire) $ python setup.py install
```

### Running

Change the password of admin account as required in the init commnd.
To run the server use the following commands:

```bash
    (certifire) $ certifire-manager init -p changeme
    (certifire) $ certifire-manager runserver
     * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
     * Restarting with reloader
```

Now the server is ready to accept requests at https://api.certifire.xyz (or whatever you have configured)

### To run certifire as a service:

```bash
    $ sudo cp certifire.service /etc/systemd/system/
    $ sudo systemctl daemon-reload
    $ sudo systemctl enable --now certifire
```

Now continue with [common setup](./#edit-nginx-configration-for-proxy)
