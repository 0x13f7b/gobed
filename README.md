# Deploying GoBed

## Prerequisites

* The database should be manually created before the first deployment
* Have the initial migrations installed
* Configure Passport
    * `php artisan passport:keys`
* The `.env` file should have valid values

## Server configuration

### Nginx

```
server {
    listen 80;
    server_name example.com;
    root /srv/example.com/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

## Autoloader Optimization

`composer install --optimize-autoloader --no-dev`*

## Migrations

`php artisan migrate`

## Passport

`php artisan passport:keys`

## Assets

### Install

`yarn run install`

### Build

`yarn run prod`

## Optimizing Configuration Loading

`php artisan config:cache`**

## Optimizing Route Loading

`php artisan route:cache`

## Optimizing View Loading

`php artisan view:cache`

## Disclaimer

\* In addition to optimizing the autoloader, you should always be sure to include a `composer.lock` file in your project's source control repository. Your project's dependencies can be installed much faster when a `composer.lock` file is present.

\** If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the env function from within your configuration files. Once the configuration has been cached, the `.env` file will not be loaded and all calls to the env function will return null.

# gobed
