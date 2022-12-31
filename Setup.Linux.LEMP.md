---
id: ur1x1chx99zo9n7760csif8
title: LEMP
desc: "Setup Nginx, MySQL and PHP"
updated: 1672486390427
created: 1672474450021
---

> [StackCoder](https://stackcoder.in/posts/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu) -- Resource Link

## **Step 1 - Install NGINX webserver**

NGINX is similar to you that of Apache webserver which handles all your server incoming requests on port 80

```shell
sudo apt-get update
sudo apt-get install nginx
```

To cross verify whether the installation is successful or not you can simply open your server ip_address in your browser as follows

http://your_server_ip_address

You will see a NGINX successfully installed page. Which loads from /var/www/html/ folder

## **Step 2 - Install the MYSQL database & secure it with a password**

MYSQL is a relational database that is used to store the dynamic data and fetch the data which will come from login, register, and various other types of forms

```shell
sudo apt-get install mysql-server
```

###

Now the MYSQL database is installed. As earlier now MYSQL won't prompt for a password. So to securely install the MYSQL server use the following commands

####

```shell
sudo mysql_secure_installation
```

###

> ### > **Please Select NO**

###

By chance, if you select YES the option then you will get the following prompt

There are three levels of a password validation policy. Select difficulty based on your requirement.

```shell
LOW  Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:
```

###

Next, add the root user password and confirm the password.

```shell
New password:

Re-enter new password:
```

For best security practices it's a good idea to press YES for the subsequent prompts. These prompts include remove anonymous users, remove the test databases, disable remote root logins and flush privileges to save changes into effect.

By default, you will be root a user with auth_socket enabled connection. This means while connecting to the database no need for any passwords. At some point later this might cause security issues. So it's better to add the password authentication for all requesting connections.

Let me show you the same. First, let's login to MYSQL

```shell
sudo mysql
```

Next, run the following to see the details of the root user

```shell
SELECT user,authentication_string,plugin,host FROM mysql.user;
```

Once you run the above command you can see that root user doesn't have the authentication_string ie password.

```shell
Output
+------------------+-------------------------------------------+-----------------------+-----------+
| user | authentication_string | plugin | host |
+------------------+-------------------------------------------+-----------------------+-----------+
| root | | auth_socket | localhost |
```

To set the password run the following command, make sure to change the your_password with the valid required password

```shell
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
```

##

> ### If want to create a remote user
>
> [[Add remote user to MySQL|Setup.Linux.MySQL.add-remote-user]]

##

Run the following command so that the changes make will take an effect.

```shell
FLUSH PRIVILEGES;
```

Now again if you re-run the above command to check if the password is set or not then it will set with mysql_native_password. No worries if your not able to see them authentication_string as it will be encrypted by MYSQL

```shell
Output
+------------------+-------------------------------------------+-----------------------+-----------+
| user       | authentication_string           | plugin        | host   |
+------------------+-------------------------------------------+-----------------------+-----------+
| root       | *549966GJ45794839F92C1571D6D78F | mysql_native_password | localhost |
```

Now you can safely exit from MYSQL prompt. If you try to log in as sudo mysql then it won't allow. You need to add your_password to do so.

## **Step 3 – Install PHP and its packages**

You are doing a great job. Now you have installed NGINX webserver and MYSQL its time to set up your PHP so that your PHP scripts run.

NGINX unlike APACHE doesn't have any native code to instruct that PHP scripts need to be processed. Now, NGINX has to pass the PHP processing requests to PHP-FPM (FAST CGI PROCESS MANAGER)

Now install all the PHP packages in the Ubuntu server with the following command. No need to add a specific version like the way I will do in the following command. But make sure that your local development and now installing PHP version matches else if you use composer in the future you will have to face some problems.

```shell
sudo apt-get install php8.1-fpm php8.1-cli php8.1-common php8.1-curl php8.1-gd php8.1-gmp php8.1-mbstring php8.1-mysql php8.1-xml php8.1-zip composer git zip unzip
```

You can add composer, zip, unzip, git in the same command instead of adding later :)

## **Step 4 - Configure PHP with NGINX webserver**

Once the PHP , MYSQL & NGINX installation is done then your left with configuring NGINX with PHP-FPM. As we had discussed earlier that NGINX by itself cannot process the PHP scripts you need to do this process.

First, you need to set up NGINX for your site. Basically, you will find all NGINX available configurations in the following path

> /etc/nginx/sites-available/

To check if any of these configurations are enabled or not then you need to check in the following path.

> NOTE: These are only symbolic links which will be pointing to your /etc/nginx/sites-available directory

> /etc/nginx/sites-enabled/

Make sure to replace your_website_name.com with your domain name in the following configuration.

```shell
sudo nano /etc/nginx/sites-available/your_domain_name.com
```

Now add the following configuration to the file. In the following, if you don't have a domain name still then just add the ip_address of your domain name.

```conf
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    root /var/www/example;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

##

Now you have added your_domain_name.com NGINX configuration lets activate it by the following command

```shell
sudo ln -s /etc/nginx/sites-available/your_domain_name.com /etc/nginx/sites-enabled/
```

###

Activating your domain name is nothing but adding the symbolic link in your /etc/nginx/sites-enabled/ folder. Which will be read by the NGINX main configuration file nginx.conf which resides in /etc/nginx/nginx.conf

As a default, NGINX will have default configuration which will be activated we have to unlink from /etc/nginx/sites-enabled/default with the following command

```shell
sudo unlink /etc/nginx/sites-enabled/default
```

##

> NOTE: Remember here we just unlinking the file. Still, your main file exists in /etc/nginx/sites-available the folder which you can activate at any given point of time with the following command

```shell
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
```

## **Step 5 - Test & restart your NGINX configuration**

This is a very important step. Don't forget to restart you NGINX server. Else the configuration that you have added won't work.

First, check if the NGINX added code to the configuration file is proper or not by the following command

```shell
sudo nginx -t
```

You must be able to see similar output

```shell
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Now reload the NGINX server configuration file with the following

```shell
sudo service nginx reload
```

###

> ### [[Certbot|Setup.Linux.Certbot]] - If want to setup SSL on the server.
