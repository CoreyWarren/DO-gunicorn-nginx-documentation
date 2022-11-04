# DO-gunicorn-nginx-documentation
DigitalOcean - Gunicorn and NginX Tutorial

## Important Files/Directories:

```
# gunicorn setup file:
/etc/systemd/system/gunicorn.service

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
