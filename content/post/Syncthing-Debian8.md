---
title: "Syncthing-Debian8"
date: 2018-08-26T11:01:55+08:00
url: "2018/08/26/Syncthing-Debian8"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/syncthing.jpeg"
---

This tutorial will teach you how to install Syncthing on Debian 8 server and use it to back up your Web site.

<!--more-->

## Install Syncthing on Debian 8  via Official Deb Repository
Use curl to download the GPG key then import the key with apt-key.
```
sudo apt-get install curl
curl -s https://syncthing.net/release-key.txt | sudo apt-key add -
```
-s option enables silent mode.

If you see OK in the terminal, that means the GPG key is successfully imported. Then add official deb repository with the following command.
```
 echo "deb http://apt.syncthing.net/ syncthing release" | sudo tee /etc/apt/sources.list.d/syncthing.list
```
Update local package index and install syncthing on Debian 8.

```
sudo apt-get update
sudo apt-get install syncthing
```

## Using Systemd to Set Up Syncthing as a System Service

Since we are installing Syncthing on a server, we set up Syncthing as a system service to ensure that Syncthing is run at startup even without an active user session.

The Syncthing system service must be started for a user with a home directory because Syncthing needs to store configuration files under the user’s home directory. To keep our Debian 8 system tidy and clean, we create a system user named syncthing with a home directory using the below command:
```
sudo useradd -r syncthing -m -d /home/syncthing
```
-r option is used to create a system user. -m option tells useradd to create a home directory. -d option specifies the home directory.

Under /lib/systemd/system/ directory, you will find a syncthing@.service file. Enable syncthing to auto start when Debian 8 is booted up by running the below command.
```
sudo systemctl enable syncthing@syncthing.service
```
The first syncthing is the service name; the second syncthing is the user we created earlier. The above command will create a symbolic link that points to the syncthing@.service file.
```
/etc/systemd/system/multi-user.target.wants/syncthing@syncthing.service -> /lib/systemd/system/syncthing@.service
```
Now let’s start the syncthing service with the following command.

sudo systemctl start syncthing@syncthing.service
Check status
```
systemctl status syncthing@syncthing.service
```
By default, Syncthing service listens on 127.0.0.1:8384. We can use curl to see if it’s working.
```
curl -I 127.0.0.1:8384
```

## Accessing the Syncthing Web Interface

Since it listens on 127.0.0.1:8384, Syncthing Web interface is only available to connections from the same computer. To be able to access the Syncthing Web interface from a remote computer, we can use Nginx to set up a reverse proxy for Syncthing. First install Nginx if haven’t already done so.
```
sudo apt-get install nginx
```
Then create a server config file. Replace syncthing.yourdomain.com with your preferred domain name.
```
sudo nano /etc/nginx/conf.d/syncthing.yourdomain.com.conf
```
Add the following content to this file. Replace syncthing.yourdomain.com with your preferred domain name.
```
server {
  listen 80;
  server_name syncthing.yourdomain.com;

  root /usr/share/nginx/syncthing;
  access_log /var/log/nginx/syncthing.yourdomain.com.log;
  error_log /var/log/nginx/syncthing.yourdomain.com.error.log;
  location / {
    proxy_pass http://127.0.0.1:8384;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
Save and close the file. Then create the Web root for syncthing.

```
sudo mkdir /usr/share/nginx/syncthing
```
Test Nginx configuration and reload Nginx.

```
sudo nginx -t
sudo systemctl reload nginx
```
After you point your domain name to the IP address of Debian 8 server, type your domain name in the browser address bar and you should see the Syncthing Web interface.
{{< gallery "/imgs/syncthing-web-interface.png" >}}

## 参考
* [How to Install Syncthing on Debian 8 Server to Back Up Website](https://www.linuxbabe.com/backup/install-syncthing-debian-8-server-back-up-website)
<!--more-->
