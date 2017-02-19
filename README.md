# docker-compose-skeleton
Ready to use docker-compose stack with php7 (php7-fpm), nginx and postgresql optimised for Symfony3 applications
(with ELK & Redis)

##Disclaimer
1. Nginx conf & docker-compose.yml assure your project is located in */var/www/* (if not make sure to change the .yml & .conf files)
2. Redis & ELK are optional

##Step by step configuration
###Step 1
make sure [docker](https://docs.docker.com/engine/installation/) and [docker-compose](https://docs.docker.com/compose/install/) are installed 

###Step 2
1. Replace every occurence of **_{PROJECT_NAME}_** by the name of you project
2. Rename **_.conf/.site-conf/{PROJECT_NAME}.conf_** to the desired configuration name
3. Replace **_{PROJECT_URL}_** by the desired url of your project.
4. Edit **_DB_USER_**, **_DB_PASS_**, **_DB_NAME_** in **_.conf/.env/db.env_**

##Step 3
update your hosts file with the new url
```bash
sudo echo 127.0.0.1 {PROJECT_URL} www.{PROJECT_URL}  >> /etc/hosts
```

###Step 4
Pull docker images
```bash
docker pull redis:alpine && docker pull willdurand/elk && docker pull php:7-fpm && docker pull nginx:latest && docker pull postgres:latest
```

###Step 5
go to /var/www/project_name & run docker-compose
```bash
docker-compose up -d
```

###Step 6
enjoy ! go to www.project_url (make sure your have something in the directory /var/www/{PROJECT_NAME}/web)

###Step 7 (optional)
Add your ip of your host (seen from your php-fpm container) in _web/config.php_ and _web/app_dev.php_ (mine was 172.18.0.1)
```php
if (!in_array(@$_SERVER['REMOTE_ADDR'], array(
    '127.0.0.1',
    '172.18.0.1', //ip to add
    '::1',
))) {
    header('HTTP/1.0 403 Forbidden');
    exit('This script is only accessible from localhost.');
}
```
If you don't know the ip to add :
```php
var_dump($_SERVER['REMOTE_ADDR']); 
if (!in_array(@$_SERVER['REMOTE_ADDR'], array(
```

###Useful commands
1. List all active containers
```bash
docker ps
```
2. List all the images available locally
```bash
docker images
```
3. Open bash in the given docker container (use docker ps to see docker id):
```bash
docker exec -it {docker_id} bash
```
4. Clear cache symfony : 
```bash
docker exec -it $(docker ps -f name=php -q) php /var/www/{PROJECT_NAME}/bin/console cache:clear 
```

4. Use psql in postgresql container : 
```bash
docker exec -it $(docker ps -f name=postgre -q) psql -U postgres
```

##Todo 
1. Test db container
2. Use php7-fpm docker images with recommended extensions and Xdebug



##Special thanks
User [Thomas Letellier](https://github.com/ltrtom) for help & support

User [Ronan Guilloux](https://github.com/ronanguilloux) for inspiration
