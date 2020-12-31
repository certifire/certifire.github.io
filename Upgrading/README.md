---
sort: 2
---

# Upgrading

```warning
Databse migrations not implemented yet. You will have to drop the database and recreate
for mid to high version chenges (eg. v0.1.0 to v0.2.0)
```

```danger
Doing this will delete all your accounts and certificates, proceed with care
```

```bash
    (certifire) $ certifire-manager drop_db
    (certifire) $ git pull
    (certifire) $ pip install -r requirements.txt
    (certifire) $ python setup.py install
    (certifire) $ certifire-manager init
    (certifire) $ certifire-manager runserver
```
