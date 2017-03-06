---
title: Nginx+Wordpress
date: 2017-03-06 17:27:21
tags: [Nginx, WordPress, mysql] 
---
# Introduction
I had a scenario where I had to setup WordPress running on an Nginx server.
So steps are as follows.

## Setting up Nginx to use PHP processors
Install PHP processors
```
sudo apt-get install php-fpm php-mysql
sudo vi /etc/php/7.0/fpm/php.ini
```
Make the following change in the file
```
cgi.fix_pathinfo=0
```
We need to give permission to the correct user, i.e. `nginx` in this case
```
sudo vi /etc/php/7.0/fpm/pool.d/www.conf
```
Now change the following line accordingly
```
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```
Restart PHP processors
```
sudo systemctl restart php7.0-fpm
```
or
```
sudo service php7.0-fpm restart
```
Now allow Nginx to use PHP, I am going to configure jishnu.me to use PHP.
```
sudo vi /etc/nginx/conf.d/jishnu.me.conf
```
Now add the following, note I changed the root directory of my location as it does not make any sense to serve from html folder
```
    location / {
        root   /var/www/bkop.in;
        index  index.php index.html index.htm;
    }

    location ~ \.php$ {
        # Zero-day exploit defense.
        # http://forum.nginx.org/read.php?2,88845,page=3
        # Won't work properly (404 error) if the file is not stored on this server, which is entirely possible with php-fpm/php-fcgi.
        # Comment the 'try_files' line out if you set up php-fpm/php-fcgi on another machine.  And then cross your fingers that you won't get hacked.
        try_files $uri =404;

        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include fastcgi_params;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        #fastcgi_intercept_errors on;
        root /var/www/bkop.in;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;

    }

    location ~ /\.ht {
        deny all;
    }
```

Now thats run, check if configuration is correct or not
```
sudo nginx -t
```
If the out put says everything is fine go ahead, else go and check the configuration, check folder permission etc.

```
sudo service nginx reload
sudo service nginx restart
```
Now lets check if the PHP is working fine in Nginx
```
sudo vi /var/www/jishnu.me/html/info.php
```
Add the following line

```
<?php phpinfo(); ?>
```

Now go to `http://jishnu.me/html/info.php` and see if the page comes up
## Setting up mysql 

Install mysql (*if you don't have that already*)
```
sudo apt-get install mysql-server
sudo mysql_secure_installation
```
Enter your password and setup the what is correct answers for your case.

Once that is done lets setup WordPress db
```
mysql -u root -p
```
Run the following commands
```
create database WordPress;

create user wpuser@localhost IDENTIFIED BY 'your-password';

grant all privileges on WordPress.* to wpuser@localhost identified by 'your-password';

flush privileges;

exit;
```

## Wordpress configuration
Once that is done, let's configure WordPress
```
cd
wget https://wordpress.org/latest.tar.gz
tar xvf latest.tar.gz
sudo mv /* /var/www/jishnu.me/
cd /var/www/jishnu.me
sudo cp wp-config-sample.php wp-config.php
sudo vi wp-config.php
```
Edit the following 
```
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'your-password');
```
Once that is done change persmission of the files 
```
sudo chown nginx:nginx /var/www/jishnu.me -R
```

Whola!!! That's it. You can use WordPress in your VPS via Nginx.

# Credits
I had got stuck with multiple times with multiple occasions before finally stumbling upon this [tutorial](https://www.linuxbabe.com/linux-server/install-wordpress-ubuntu-16-04-nginx-mariadb-php7). So do check them out if you are stuck.