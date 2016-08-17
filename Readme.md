# Docker use cases
## Docker container as a service

Restart policy option for `run` command

docker run --restart=[unless-stopped/always] [image_name]

read more: https://docs.docker.com/engine/reference/run/#restart-policies-restart

### Example deamon
Simple container

```
docker run --name docker_daemon -d ubuntu ping google.com`
```

Show container logs
```
docker logs -f docker_daemon
```
Simple daemon container
```
docker run --name docker_daemon -d --restart=unless-stopped ubuntu ping google.com
```

### Dockerized mysql deamon
Run mysql container with restart
```
docker run --name docker-mysql --restart=unless-stopped -v ./mysql:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=drupal -d mysql
```

Call mysql commain inside container
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
docker run --rm -v $(pwd):/work docker-phpcs-drupal custom_module
```

To call phpcbf you need rewrite entrypoint directive
```
docker run --rm -v $(pwd):/work --entrypoint=phpcbf docker-phpcs-drupal --standard=Drupal ./
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
    && ln -s /root/.composer/vendor/drupal/coder/coder_sniffer/Drupal /root/.composer/vendor/squizlabs/php_codesniffer/CodeSniffer/Standards/Drupal

# Set environment directives
VOLUME /work
WORKDIR /work
ENTRYPOINT ["phpcs", "--standard=Drupal"]
```

#### Building image process
```
docker build -t dcafe-image .
```

## Local dev environment based on docker
### Simple drupal env with mysql php
To make simple env we need to create two containers for php and db

#### Mysql
```
docker run --name docker-mysql --restart=unless-stopped -v ./mysql:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=drupal -d mysql
```

#### Php
```
docker run --name docker-php -d -v $(pwd)/drupal:/srv -p 8000:80 --link docker-mysql:mysql skilldlabs/php:7
```

### Docker compose magic

```
TBD
```

### Open source dockerized drupal

* https://dockerizedrupal.com
* http://docker4drupal.org/


## Building anf testing project with docker
### PHPUnit

## Tips

Run php simple server
```
php -S localhost:8000
```

Install drupal site via drush
```
drush si --db-url=mysql://root:root@localhost/drupal -y --notify
```
