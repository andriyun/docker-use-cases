* Docker container as a service

Restart policy option for `run` command

docker run --restart=[unless-stopped/always] [image_name]

'read more' https://docs.docker.com/engine/reference/run/#restart-policies-restart

** Example deamon
Simple deamon
docker run --name ubuntu_test -d ubuntu ping google.com
docker logs -f docker_daemon
docker run --name docker_daemon -d --restart=unless-stopped ubuntu ping google.com

Dockerized mysql deamon
------------------------

Run mysql container with restart
`docker run --name docker-mysql --restart=unless-stopped -v ./mysql:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=drupal -d mysql`

Call mysql commain inside container
`docker exec -it docker-mysql mysql -uroot -proot`

Alias for mysql container
`alias dmysql="docker exec -it docker-mysql mysql -uroot -proot"`


php -S localhost:8000
drush si --db-url=mysql://root:root@localhost/drupal -y --notify
