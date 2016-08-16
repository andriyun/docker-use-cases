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
### Dockerized Compass
### Dockerized Php Code Sniffer

## Local dev environment based on docker
### Simple drupal env with mysql php
### Open source dockerized drupal

## Building anf testing project with docker
### Selenium
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
