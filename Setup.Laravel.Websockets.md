---
id: y6elnyzqw5gvt5yddpkey7e
title: Websockets
desc: ""
updated: 1674288827834
created: 1672740879128
---

Installation
Laravel WebSockets can be installed via composer:

```shell
composer require beyondcode/laravel-websockets
```

The package will automatically register a service provider.

This package comes with a migration to store statistic information while running your WebSocket server. You can publish the migration file using:

```shell
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="migrations"
```

Run the migrations with:

```shell
php artisan migrate
```

Next, you need to publish the WebSocket configuration file:

```shell
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="config"
```

##

> Note: There is some issue in the new pusher version which is not patched yet so have to downgrade the package:
> Link https://laracasts.com/discuss/channels/laravel/laravel-websocket-in-laravel-9-not-working

> ```shell
> composer require pusher/pusher-php-server:7.0.2
> ```

###

> ### .env

```env
BROADCAST_DRIVER=pusher

PUSHER_APP_ID=123456
PUSHER_APP_KEY=ABCDEF
PUSHER_APP_SECRET=TEMPSTRINGHARDTOGUESS
PUSHER_HOST=127.0.0.1
PUSHER_PORT=6001
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

LARAVEL_WEBSOCKETS_SSL_LOCAL_CERT=/etc/letsencrypt/live/example.com/fullchain.pem
LARAVEL_WEBSOCKETS_SSL_LOCAL_PK=/etc/letsencrypt/live/example.com/privkey.pem
```

##

> ### config/broadcasting.php

```php
'pusher' => [
    'driver' => 'pusher',
    'key' => env('PUSHER_APP_KEY'),
    'secret' => env('PUSHER_APP_SECRET'),
    'app_id' => env('PUSHER_APP_ID'),
    'options' => [
        'cluster' => env('PUSHER_APP_CLUSTER'),
        'encrypted' => true,
        'host' => '127.0.0.1',
        'port' => 6001,
        'scheme' => 'https',
        'curl_options' => [
            CURLOPT_SSL_VERIFYHOST => 0,
            CURLOPT_SSL_VERIFYPEER => 0,
        ]
    ],
    'client_options' => [
        // for self signed ssl cert
        'verify' => false, // <- Added this
        // Guzzle client options: https://docs.guzzlephp.org/en/stable/request-options.html
    ],
]
```

##

> ### config/websockets.php

```php
/*
 * Set a custom dashboard configuration
 */
'dashboard' => [
    'port' => env('LARAVEL_WEBSOCKETS_PORT', 6001),
],

'apps' => [
    [
        'id' => env('PUSHER_APP_ID'),
        'name' => env('APP_NAME'),
        'key' => env('PUSHER_APP_KEY'),
        'secret' => env('PUSHER_APP_SECRET'),
        'path' => env('PUSHER_APP_PATH'),
        'capacity' => null,
        'enable_client_messages' => true,
        'enable_statistics' => true,
        'encrypted' => true,
    ],
],

'ssl' => [
    /*
     * Passphrase for your local_cert file.
     */
    'passphrase' => env('LARAVEL_WEBSOCKETS_SSL_PASSPHRASE', null),

    'verify_peer' => false,

    'allow_self_signed' => true,
]
```

##

> ### Install pusher.js and laravel-echo library

```shell
npm install --save-dev laravel-echo pusher-js
```

##

> ### bootstrap.js

```js
import Echo from "laravel-echo";
import Pusher from "pusher-js";

window.Pusher = Pusher;

window.Echo = new Echo({
  broadcaster: "pusher",
  key: import.meta.env.VITE_PUSHER_APP_KEY,
  cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
  encrypted: true,
  wsHost: window.location.hostname,
  wsPort: 6001,
  wssPort: 6001,
  forceTLS: true,
  disableStats: false,
  enabledTransports: ["ws", "wss"],
});
```

Once installed, you can start it with one simple command:

```shell
php artisan websockets:serve
```

##

> ### Nginx - sites-available/example.com

```conf
map $http_upgrade $type {
    default "web";
    websocket "websocket";
}

server {
    server_name example.com www.example.com;
    root /var/www/paul/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files /dev/null @$type;
    }

    location @web  {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location @websocket  {
        proxy_pass             http://127.0.0.1:6001;
        proxy_set_header Host  $host;
        proxy_read_timeout     60;
        proxy_connect_timeout  60;

        # Allow the use of websockets
        proxy_http_version 1.1;
        proxy_set_header Upgrade 'websocket';
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}
```

### On Debian / Ubuntu

```shell
apt install supervisor
```

### On Red Hat / CentOS

```shell
yum install supervisor
systemctl enable supervisord
```

> Once installed, add a new process that supervisor needs to keep running. You place your configurations in the /etc/supervisor/conf.d (Debian/Ubuntu) or /etc/supervisord.d (Red Hat/CentOS) directory.

Within that directory, create a new file called websockets.conf.

```conf
[program:websockets]
process_name=%(program_name)s
command=sudo php artisan websockets:serve --port=6001
directory=/var/www/html/PROJECT_NAME_HERE
autostart=true
autorestart=true
user=root
redirect_stderr=true
stdout_logfile=/var/www/html/PROJECT_NAME_HERE/storage/logs/websockets.log
```

Once created, instruct supervisor to reload its configuration files (without impacting the already running supervisor jobs).

```shell
supervisorctl update
supervisorctl start websockets
```
