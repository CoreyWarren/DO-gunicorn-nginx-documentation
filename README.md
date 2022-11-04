# DO-gunicorn-nginx-documentation
DigitalOcean - Gunicorn and NginX Tutorial

## Relevant Tutorials:

```
# Primary nginx/gunicorn tutorial (has postgres stuff, but can be skipped around):
https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04
```

## Important Files/Directories:

```
# gunicorn setup file:
/etc/systemd/system/gunicorn.service

#

#

#
```

## Checking statuses of important services:

```
# Check status of gunicorn:
sudo systemctl status gunicorn

#

#

#
```

## Changing ownership of a folder/file:

```
chown root /u
#Change the owner of /u to "root".

chown root:staff /u
#Likewise, but also change its group to "staff".

chown -hR root /u
#Change the owner of /u and subfiles to "root".
```

## When you change gunicorn.service file:

```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```
