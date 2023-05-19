# DO-gunicorn-nginx-documentation
DigitalOcean - Gunicorn and NginX Tutorial

# Tutorial

#

## 1.1: Setting up your domain name and such:

To configure your DigitalOcean server to have SSH and DNS point to its www address, such as coldcmerch.com, you need to follow these general steps:

#

### 1.1.1 - DNS Configuration:

- Log in to your DNS registrar or hosting provider where you manage the DNS records for coldcmerch.com.
- Create an "A" record or edit the existing one for the "www" subdomain.
- Set the IP address of your DigitalOcean server as the destination for the "www" subdomain.

#

### 1.1.2 - Server Configuration:

- Connect to your DigitalOcean server via SSH using its IP address.
- Update the server's host file to associate the hostname with the loopback IP address.
- Open the hosts file with the command: 
``` sudo nano /etc/hosts ```
- Add an entry at the end of the file like this: 
``` 127.0.0.1 coldcmerch.com www.coldcmerch.com```
- Save the changes and exit the editor.
- Update the server's hostname:
- - Execute the command: 
```sudo hostnamectl set-hostname coldcmerch.com```
- This will set the server's hostname to "coldcmerch.com".
- Restart the server networking for the changes to take effect:
- Run the command: 
``` sudo systemctl restart systemd-networkd ``` (Works for Ubuntu 23, will be different for older versions of Ubuntu/Linux)

#

### 1.1.3 - SSH Configuration:

- Open the SSH configuration file using the command: ``` sudo nano /etc/ssh/sshd_config```
- Locate the line that starts with ```#ListenAddress```.
- Uncomment it by removing the '#' at the beginning of the line.
- Update the line to listen on the ```IP address``` of your DigitalOcean server.
- Save the changes and exit the editor.
- Restart the SSH service for the changes to take effect:
- - Execute the command: ```sudo /etc/init.d/ssh restart```
- Once these steps are completed, your DigitalOcean server's SSH and DNS should point to its www address, allowing you to SSH into the server using "coldcmerch.com" or "www.coldcmerch.com" instead of the IP address.

#

## 2.1 - nginx

# 

## 3.1 - Gunicorn

https://stackoverflow.com/questions/39460892/gunicorn-no-module-named-myproject

Note that DNS changes can take some time to propagate, so it may take a while for the changes to take effect globally.

## 3.2 - Gunicorn sucks

Here's a legend to help you figure out what each field should be in ``` /etc/systemd/system/gunicorn.service ```:

- **WorkingDirectory** -> Where your django APP is located (the whole app, not the subfolder within the app that shares its name, nor the parent folder holding the Django app which has some arbitrary name like 'django_stuff')

- **my_project.sock** -> Goes in the exact same folder as above, which is your django APP folder. Not its subfolders.

- **my_project.wsgi** -> From my understanding, there's no need to add any prefixes or parent directories to this, just keep it as ``` my_project.wsgi ``` (with your actual project name ofc), but everything around it will need to be properly directed to for this to be recognized.


### In the end, you may end up with something LIKE this, but not exactly like this. Use the above tips to get it right:

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=coco
Group=www-data
WorkingDirectory=/home/coco/coldcmerch.com/django/coldcmerch
ExecStart=/home/coco/coldcmerch.com/django/env/bin/gunicorn --access-logfile - --workers 3 --bind unix:/home/coco/coldcmerch.com/django/coldcmerch/coldcmerch.sock coldcmerch.wsgi:application


[Install]
WantedBy=multi-user.target****
```

## 4.1 - Deploying Express:

In a production environment, you typically want your Node.js applications (like your Express server) to run in the background, start automatically when the server boots up, and restart automatically if they crash. There are several ways to do this, but one of the most common methods is to use a process manager like PM2.

PM2 is a production process manager for Node.js applications with a built-in load balancer. It keeps your applications alive forever, reloads them without downtime, helps you to manage application logging, and more.

1. First, install PM2 globally on your server with npm:
    ```bash
    sudo npm install -g pm2
    ```

2. Navigate to the folder containing your Express server and start it with PM2:
    ```bash
    cd /path/to/your/express/server
    pm2 start index.js --name "my-express-app"
    ```
    Replace `index.js` with the entry point to your Express application, and replace `"my-express-app"` with a name of your choosing.

3. PM2 will now keep your Express server running in the background. You can check the status of your applications with:
    ```bash
    pm2 list
    ```
    And view their logs with:
    ```bash
    pm2 logs
    ```

4. To ensure that your Express server (and any other applications managed by PM2) start automatically on boot, you can use the `pm2 startup` command, which will provide instructions based on your system.
    ```bash
    pm2 startup
    ```

Remember, your Express server should be running on its own port (like 5000), and your Nginx configuration should include a reverse proxy to that port for the appropriate routes.





# Word to the wise (502 ERROR):

Seems like folder permissions and ownership are INCREDIBLY IMPORTANT to getting nginx and gunicorn to host the website. 

SO. Here are the problems I ran into when trying to set up nginx and gunicorn overall:

## 1 - STUPID TYPOS
Seriously, check for stupid typos first. Sometimes you may forget to replace the 'myproject' with your real project name, or you may have created a file ```/home/coco.conf.conf ```when you meant to create ```/home/coco/conf.conf```.

## 2 - PERMISSIONS PART 1 - OWNERSHIP
Refer to the 'chown' section somewhere below regarding changing ownership of folders and files.
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
sudo chmod o=r+x /home/coco
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
