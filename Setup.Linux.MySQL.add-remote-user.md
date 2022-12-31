---
id: sh50y35ya8gdphtahg49y0c
title: Add remote user to MySQL
desc: ""
updated: 1672480006275
created: 1672479349398
---

```shell
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Navigate to the line that begins with the bind-address directive. It will look like this:

```conf
. . .
lc-messages-dir = /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 127.0.0.1
. . .
```

###

By default, this value is set to 127.0.0.1, meaning that the server will only look for local connections. You will need to change this directive to reference an external IP address. For the purposes of troubleshooting, you could set this directive to a wildcard IP address, either \*, ::, or 0.0.0.0:

```conf
. . .
lc-messages-dir = /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
. . .
```

##

> Note: In certain versions of MySQL the bind-address directive may not be in the mysqld.cnf file by default. In this case, add the following highlighted line to the bottom of the file:

> **/etc/mysql/mysql.conf.d/mysqld.cnf**

```shell
[mysqld]
pid-file = /var/run/mysqld/mysqld.pid
socket = /var/run/mysqld/mysqld.sock
datadir = /var/lib/mysql
log-error = /var/log/mysql/error.log
bind-address = 0.0.0.0
```

After changing this line, save and close the file (CTRL + X, Y, then ENTER if you edited it with nano).

Then restart the MySQL service to put the changes you made to mysqld.cnf into effect:

```shell
sudo systemctl restart mysql
```

###

You can create a new user account that will only connect from the remote host with the following command

```shell
CREATE USER 'remote'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pass';
CREATE USER 'remote'@'%' IDENTIFIED WITH mysql_native_password BY 'pass';
```

Then:

```shell
GRANT ALL ON *.* TO 'remote'@'localhost';
GRANT ALL ON *.* TO 'remote'@'%';
FLUSH PRIVILEGES;
exit
```
