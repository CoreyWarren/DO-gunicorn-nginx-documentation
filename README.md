# DO-gunicorn-nginx-documentation
DigitalOcean - Gunicorn and NginX Tutorial

# Word to the wise:

Seems like folder permissions and ownership are INCREDIBLY IMPORTANT to getting nginx and gunicorn to host the website. 

SO. Here are the problems I ran into when trying to set up nginx and gunicorn overall:

## 1 - STUPID TYPOS
Seriously, check for stupid typos first. Sometimes you may forget to replace the 'myproject' with your real project name, or you may have created a file ```/home/coco.conf.conf ```when you meant to create ```/home/coco/conf.conf```.

## 2 - PERMISSIONS PART 1 - OWNERSHIP
This mostly applies to DATABASE bugs. But if you're experiencing nginx bugs with what you think is maybe 'ownership, try...:

## 3 - PERMISSIONS PART 2 - USER, GROUP, WORLD/OTHER
Make sure that nginx is able to access your .sock file by checking with
```
namei -nom /home/my_user/my_project/my_project.sock
```
The above will display the ownership, and more importantly, RIGHTS to all folders leading up to your sock file. Yes, these all count.

The solution to my problem? For some reason, my 'user' folder had 'other' permissions set to 'r--', which means yes read, no write, no execute.

I need it to be 'r-x' all the way up to my .sock file, which means I needed to use this command on my 'coco' user directory/folder:
```
sudo chmod o=r+x coco
namei -nom /home/coco 
```

## Relevant Tutorials:

### Primary nginx/gunicorn tutorial (has postgres stuff, but can be skipped around):
https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04

### Firewalls (UFW):
https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-20-04

### Solo NGINX Tutorial:
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04

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

sudo chown $USER:$USER -R this_subdirectory
#Change the ownership of this_subdirectory

```

### File PERMISSIONS (chmod):
https://www.guru99.com/file-permissions.html


## Troubleshooting Links:

### Nginx - 502 Bad Gateway Error:
https://www.digitalocean.com/community/questions/502-bad-gateway-nginx-2

### Linux Server Trouble Tips:
https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-site-issues-on-a-linux-server

## Important Files/Directories:

```
# gunicorn setup file:
/etc/systemd/system/gunicorn.service

# nginx setup for your project:
/etc/nginx/sites-available/myproject

#

#
```

# Editing Important Files:

Editing the nginx config:
```
sudo nano /etc/nginx/nginx.conf
```

## Checking statuses of important services:


### Check status of gunicorn:
```
sudo systemctl status gunicorn
sudo journalctl -u gunicorn
```

### Check nginx status:
```
sudo nginx -t
```

nginx access logs:
```
sudo less /var/log/nginx/access.log
```

nginx process logs:
```
sudo journalctl -u nginx
```

nginx error logs:
```
sudo tail -F /var/log/nginx/error.log
```

### ufw

```
sudo ufw status
```


# Refreshing:

When you change gunicorn.service file:
```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```

To restart nginx:
```
sudo systemctl restart nginx
```


# Aftermath - Finalizing server with HTTPS (Certbot, Snap):

https://snapcraft.io/docs/installing-snapd

https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal

# File too Large (413 Request Entity Too Large):

https://stackoverflow.com/questions/66929579/django-nginx-413-request-entity-too-large

```
sudo nano /etc/nginx/nginx.conf
client_max_body_size 200M;
```
