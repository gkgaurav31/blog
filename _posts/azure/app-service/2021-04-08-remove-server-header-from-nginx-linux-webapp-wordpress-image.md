---
layout: post
title: Remove Server Header from Nginx (Linux WebApp - Wordpress Image)
date: 2021-04-08 18:32 +0530
---

The Docker Image [appsvcorg/wordpress-alpine-php:0.72](https://hub.docker.com/r/appsvcorg/wordpress-alpine-php) for WordPress uses [Nginx](https://www.nginx.com/). In order to remove the [Server header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Server) of Nginx, you can follow the below steps:

## CREATE A DOCKERFILE

```Dockerfile
FROM appsvcorg/wordpress-alpine-php:0.72
RUN apk update && apk add nginx-mod-http-headers-more
COPY nginx.conf /etc/nginx/
```

## CREATE nginx.conf file

```nginx.conf
user  nginx;
worker_processes  auto;

load_module /usr/lib/nginx/modules/ngx_http_headers_more_filter_module.so;

error_log  /var/log/nginx/error.log error;
pid        /var/run/nginx.pid;


events {
    worker_connections  2048;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    more_clear_headers 'Server';
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  off;

    sendfile        on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/modules-enabled/*.conf;
}
```

## BUILD THE IMAGE (AND PUSH IT TO YOUR DOCKER REGISTRY)

docker build -t myapp .
docker rm -f test
docker run -d -p 8080:80 --name test myapp

## YOU CAN USE CURL TO TEST LOCALLY AS WELL

```curl
curl http://localhost:8080 -I
HTTP/1.1 302 Found
Date: Thu, 08 Apr 2021 16:38:35 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
X-Powered-By: PHP/7.2.13
Location: http://localhost:8080/wp-admin/setup-config.php
Fastcgi-Cache: MISS
X-Xss-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
```

[SOURCE CODE](https://github.com/gkgaurav31/wordpress-alpine-php-0.72-test.git)

That's it. :v:
