# DO-gunicorn-nginx-documentation
DigitalOcean - Gunicorn and NginX Tutorial

## Relevant Tutorials:

### Primary nginx/gunicorn tutorial (has postgres stuff, but can be skipped around):
https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04

### chown (changing ownership in Ubuntu/Debian):
https://linux.die.net/man/1/chown

```
-R, --recursive
# operate on files and directories recursively

chown root /u
#Change the owner of /u to "root".

chown root:staff /u
#Likewise, but also change its group to "staff".

chown -hR root /u
#Change the owner of /u and subfiles to "root".

```


## Troubleshooting Links:

### Nginx - 502 Bad Gateway Error:
https://www.digitalocean.com/community/questions/502-bad-gateway-nginx-2


## Important Files/Directories:

```
# gunicorn setup file:
/etc/systemd/system/gunicorn.service

# nginx setup for your project:
/etc/nginx/sites-available/myproject

#

#
```

## Checking statuses of important services:

```
# Check status of gunicorn:
sudo systemctl status gunicorn

# check nginx status:
sudo nginx -t

# check nginx specific errors:
sudo tail -F /var/log/nginx/error.log

#
```

## Refreshing your services after Changes:

When you change gunicorn.service file:
```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```

To restart nginx:
```
sudo systemctl restart nginx
```
