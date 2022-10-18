```
version: "3"

services:
  nginx:
    image: nginx:stable
    container_name: nginx
    restart: always
    ports:
      - "9601:9601"
      - "9602:9602"
    volumes:
      - /var/log/nginx:/var/log/nginx
      - /etc/nginx/conf.d:/etc/nginx/conf.d
      - /var/www/html:/var/www/html
    networks:
      - my-lnmp
    deploy:
      mode: replicated
      replicas: 3

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - "13306:3306"
    volumes:
      - /etc/mysql:/etc/mysql/mysql.conf.d
      - /var/lib/mysql:/var/lib/mysql
      - /var/log/mysql:/var/log
    environment:
      MYSQL_ROOT_PASSWORD: "mypass"
    networks:
      - my-lnmp
    deploy:
      placement:
        constraints: [ node.role == manager ]

  redis:
    image: redis:7.0
    container_name: redis
    restart: always
    ports:
      - "16379:6379"
    volumes:
      - /usr/local/redis/data/:/data
    networks:
      - my-lnmp
    deploy:
      placement:
        constraints: [ node.role == manager ]

  php:
    image: php73
    container_name: php
    restart: always
    volumes:
      - /var/www/html:/var/www/html
    networks:
      - my-lnmp
    deploy:
      mode: replicated
      replicas: 3

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "18080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    networks:
      - my-lnmp
    deploy:
      placement:
        constraints: [ node.role == manager ]

networks:
  my-lnmp:
```