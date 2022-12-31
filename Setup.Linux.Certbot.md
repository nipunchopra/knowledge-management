---
id: rcohd1e53b7jyjgvcrn4q5i
title: Certbot
desc: "Setyp Certbot for SSL on Apache and Nginx Server"
updated: 1672486128715
created: 1672485230506
---

> [StackCoder](https://stackcoder.in/posts/how-to-create-free-ssl-certificate-with-lets-encrypt-certbot-in-linux-single-multiple-domains) -- Resource Link

## **Step 1 - Add Certbot PPA (Apache & Nginx)**

Now add Certbot PPA to your list of repositories

APACHE Configurations

```shell
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
```

NGINX Configurations

```shell
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
```

## **Step 2 - Install Certbot**

Run the following command to install Certbot

APACHE COMMAND

```shell
sudo apt-get install certbot python-certbot-apache
```

NGINX COMMAND

```shell
sudo apt-get install certbot python-certbot-nginx
```

## **Step 3 - Run Certbot**

To run the Certbot there are 2 ways:

> Automatic Installation By Certbot
> This will automatically install Certbot and install HTTPS automatically

APACHE Installation

```shell
sudo certbot --apache
```

NGINX Installation

```shell
sudo certbot --nginx
```

###

> NOTE: If your running the above command for the 1st time then it will ask you to

- Agree terms & condition - (A)
- Share Email - Yes / No as per your preferences
