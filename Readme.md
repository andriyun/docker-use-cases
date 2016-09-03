# Docker use cases
## Docker container as a service

Restart policy option for `run` command

read more: https://docs.docker.com/engine/reference/run/#restart-policies-restart

### Common commands
Simple container
```
docker run -it centos
```

### Simple deamon container in detached mode
```
docker run --name docker_daemon -d \
  --restart=unless-stopped \
  -v $(pwd)/service:/scripts \
  centos \
  /scripts/service.sh
```
Cons: it doesn't work after restart.

####Show containers status
```
docker ps
```

####Show container logs
```
docker logs -f docker_daemon
```

####Stop and remove docker container
```
docker stop docker_daemon && docker rm docker_daemon
docker rm -f docker_daemon
```

### Dockerized mysql deamon
Run mysql container with restart
```
docker run --name docker-mysql -d \
  --restart=unless-stopped \
  -v $(pwd)/mysql:/var/lib/mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=drupal \
  mysql
```
read more: https://hub.docker.com/_/mysql/

Call mysql command inside container
```
docker exec -it docker-mysql mysql -uroot -proot
```

Alias for mysql container
```
alias dmysql="docker exec -it docker-mysql mysql -uroot -proot"
```

## Docker container as a tool

Use required tool or process inside container and receive result via mounted volumes.

Common way:
```
docker run --rm -v $(pwd):/mounted_dir tool-contaner [args]
```
### Dockerized Php Code Sniffer

Call phpcs from container
```
cd /path/to/the/code
docker run --rm -v $(pwd):/work phpcs-drupal
```

To call phpcbf you need rewrite entrypoint directive
```
docker run --rm -v $(pwd):/work phpcs-drupal phpcbf --standard=Drupal ./
```

### Dockerfile magic

Dockerfile content
```
# Parent container
FROM php:7.0-alpine

# Setup required packages
RUN curl -sS https://getcomposer.org/installer | php -- --filename=composer --install-dir=/usr/bin
RUN composer global require drupal/coder \
    && ln -s /root/.composer/vendor/squizlabs/php_codesniffer/scripts/phpcs /usr/bin/phpcs \
    && ln -s /root/.composer/vendor/squizlabs/php_codesniffer/scripts/phpcbf /usr/bin/phpcbf \
    && ln -s /root/.composer/vendor/drupal/coder/coder_sniffer/Drupal /root/.composer/vendor/squizlabs/php_codesniffer/CodeSniffer/Standards/Drupal
RUN apk add --no-cache patch

# Set environment directives
VOLUME /work
WORKDIR /work
CMD ["phpcs", "--standard=Drupal", "."]
```

#### Building image process
```
cd path/to/Dockerfile
docker build -t phpcs-drupal .
```

## Local dev environment based on docker
### Simple drupal env with mysql php
To make simple env we need to create two containers for php and db

#### Mysql
```
docker run --name mysql --restart=unless-stopped -v $(pwd)/mysql:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=drupal -d mysql
```

#### Php
```
docker run --name php -d -v $(pwd)/drupal:/srv -p 8000:80 --link mysql:mysql php:7.0-alpine php -t /srv -S 0.0.0.0:80
```

### Docker compose magic

docker-compose.yml file
```
version: "2"

services:
  web:
    image: skilldlabs/php:7
    ports:
      - "8000:80"
    volumes:
      - ./drupal:/srv
    links:
      - mysql:mysql
    depends_on:
      - mysql
    restart: always

  mysql:
    image: mysql
    volumes:
      - ./db:/var/lib/mysql
    environment:
      MYSQL_DATABASE: d8
      MYSQL_USER: d8
      MYSQL_PASSWORD: d8
      MYSQL_ROOT_PASSWORD: d8root
    restart: always
```

#### Compose docker containers
To run environment from docker-compose.yml file just call
```
cd path/to/docker-compose/file
docker-compose up
```

### Open source dockerized drupal

* http://docker4drupal.org/
* https://dockerizedrupal.com

## Useful links
* [docs.docker.com](https://docs.docker.com/)
* [Get started tutorial](https://docs.docker.com/engine/getstarted/)
* [Official docker registry](https://hub.docker.com/)
* [15 Docker Tips in 5 Minutes](https://speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)
* [Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet)

## Tips

```
# Start simple php dev server
php -S localhost:8000

# Usefull aliases
alias allismine="sudo chown -R $(whoami):$(whoami) *"
alias docker-washup="docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q)"
```
free -h
