---
layout:         post
title:          'Nginx Configuration'
excerpts:       ''
private:        true
---

## NodeJS + PM2

`nginx.conf`:

```sh
upstream node {
    server 127.0.0.1:3000;
}
server {
    listen       80;
    server_name example.com;

    location / {
        proxy_pass    http://node;
    }
}
```

## Wordpress + Docker Compose

`nginx.conf`:

```sh
server {
    listen       80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_buffers 16 4k;
        proxy_buffer_size 2k;
        proxy_set_header Host $http_host;
    }
}
```

`docker-compose.yml`:

```yml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```