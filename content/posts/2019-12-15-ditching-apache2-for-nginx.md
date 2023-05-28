---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2019-12-15T16:24:10+00:00'
guid: https://nuculabs.dev/?p=624
id: 624
image: /wp-content/uploads/2019/12/b7160-nginx.png
layout: post
permalink: /2019/12/15/ditching-apache2-for-nginx/
tags:
- nginx
title: Ditching Apache2 for Nginx
---
Hello,


I wanted to self host my [WordPress](https://wordpress.org/) blog and for that I’ve chosen a hosting provider which I no longer recommend..


Because I’m lazy and I don’t know how to configure web servers that well I’ve used XXX’s WordPress one click install, the package installed WordPress and did the following things:


- Enables the UFW firewall to allow only SSH (port `22`, rate limited), HTTP (port `80`), and HTTPS (port `443`) access.
- Sets the MySQL root password, runs `mysql_secure_installation`, and creates a `wordpress` user with the necessary permissions.
- Sets up the `debian-sys-maint` user in MySQL so the system’s init scripts for MySQL will work without requiring the MySQL `root` user password.
- Creates the initial WordPress configuration file to set up [salt keys](https://developer.wordpress.org/reference/functions/wp_salt/) and allow the WordPress instance to connect to the database.
- Disables XML-RPC to help prevent DDoS and other brute force attacks. (Should you require xmlrpc, run “a2disconf block-xmlrpc” from the terminal to disable blocking
- Modifies some of PHP’s settings to increase the maximum filesize and execution time.
- Enables [the Apache rewrite module](https://httpd.apache.org/docs/current/mod/mod_rewrite.html) so the WordPress permalink feature will work.
- Configures Apache with `UseCanonicalName On` to mitigate [CVE-2017-8295](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-8295).


This is pretty convenient as you can setup a fully functional blog in less than 10 minutes, the only thing that is missing is a email configuration.


But, there’s is a problem. **Apache2** is old, harder to configure, secure and maintain… I didn’t like it so I wanted to replace it with [**Nginx**](https://www.nginx.com/).


- - - - - -


## Initial Preparation


The first step I did was to turn off Apache2 and block the web ports via XXX’s cloud firewall. Blocking the ports is important because I don’t want to serve my initial WordPress configuration file as a txt by accident.


```
```bash
sudo systemctl stop apache2
```
```


Next, I’ve installed the necessary dependencies:


```
```bash
sudo apt update
sudo apt install nginx
sudo apt install python-certbot-nginx
sudo apt install php7.2-cli php7.2-fpm php7.2-mysql php7.2-json php7.2-opcache php7.2-mbstring php7.2-xml php7.2-gd php7.2-curl
```
```


## Configuring Nginx


I’ve deleted the default configuration file from Nginx and created the configuration file for my WordPress blog.


```
```bash
sudo rm /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default
sudo touch /etc/nginx/sites-available/wp-blog
sudo ln -s /etc/nginx/sites-available/wp-blog /etc/nginx/sites-enabled/
```
```


Open **/etc/nginx/sites-available/wp-blog** paste the following things:


```
```
# Redirect HTTP -> HTTPS
server {
    listen 80;
    server_name www.domain.tld domain.tld;
    include snippets/letsencrypt.conf;
    return 301 https://domain.tld$request_uri;
}
# Listen to HTTPS
server {
    listen 443 ssl http2;
    server_name domain.tld;
    root /var/www/html;
    index index.php;
    # SSL parameters
    ssl_certificate /etc/letsencrypt/live/domain.tld/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/domain.tld/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/domain.tld/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;
    # log files
    access_log /var/log/nginx/domain.tld.access.log;
    error_log /var/log/nginx/domain.tld.error.log;
    location = /favicon.ico {
        log_not_found off;
        access_log off;
    }
    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~ .php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
    location ~* .(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires max;
        log_not_found off;
    }
}
```
```


Next, create **/etc/nginx/snippets/letsencrypt.conf** and paste:


```
```
location ^~ /.well-known/acme-challenge/ {
  allow all;
  root /var/lib/letsencrypt/;
  default_type "text/plain";
  try_files $uri =404;
}
```
```


And finally create **/etc/nginx/snippets/ssl.conf** and paste:


```
```
ssl_dhparam /etc/ssl/certs/dhparam.pem;
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:50m;
ssl_session_tickets off;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
ssl_prefer_server_ciphers on;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 30s;
add_header Strict-Transport-Security "max-age=15768000; includeSubdomains; preload";
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
```
```


The last thing we need is to create the **dh-param.pem** file:


```
```
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```
```


Now, to test that our configuration is valid we run **nginx -t**. You shouldn’t see any errors.


Note: *You should replace the **domain.tld** with our own domain. I already had the Let’s Encrypt certificate on the machine, if you don’t have one then you should generate one using certbot.*


## Uninstalling Apache2


You should start the website and check if it’s running correctly, then you may uninstall apache2.


```
```
sudo apt-get purge apache2
sudo rm -rf /etc/apache2 
```
```


## Conclusions


I got rid of Apache2 in less than 20 minutes and the blog is up and running. If you’re going to attempt to do this please make a backup first!


Thanks for reading!


![](https://media.giphy.com/media/EqGGGbjjFltjG/giphy.gif)
### Resources


- 
- 