# Docker
A basic docker isntallation and knowledges  
## Content
[Usage](#Usage)  
[Errors](#Errors-and-fix)  
[Knowledge](#Define-build)  
## Requirements
#### Window
Install [Docker](https://docs.docker.com/docker-for-windows/install/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
#### Macos
Install [Docker](https://docs.docker.com/docker-for-mac/install/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
#### Linux
Install [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
## Usage
1. Create new ```source``` floder
2. Clone project's source into the source folder
3. Edit docker-compose.yml file
```
```
4. Edit nginx/default.conf (optional)
5. Build
```php
sudo docker-compose up --build
```
## Errors and fix
### Changing mysql username and password
#### Scenario When you try to edit docker yml script to change mysql user and re-build docker container
```php
 db:
    image: mysql:5.7
    volumes:
      - db_data1:/var/lib/mysql
    ports:
      - 32011:3201
    environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: local
       MYSQL_USER: root
       MYSQL_PASSWORD: root
    networks:
      - appnet
```
Docker will run as normal and no user/password was changed/created due to docker-compose does extra work to preserve volumes between runs
#### Solution
**For new container and doesnt have anything init**
```docker-compose rm -v``` delete docker managed volumes, but **NOT** bind mounted "volumes".
**For db that already in function**
You sould try to access the db manually using the old accoutn and create a new one
#### Exit code 0: 
If you get the container 
```exit with code 0``` 
that mean your container have finish all it's work and exit as docker natural habit. To keep it up all time add **tty: true** will keep it up and allow you to access
###  SQLSTATE[HY000] [2002] Connection refused
This due to your .env host is incorrect. Mysql container run in local host **NOT** mysql service, so you need to referal to that container name/ip instead of localhost/127.0.0.1
**Sample in magento2**
```php
'table_prefix' => '',
        'connection' => [
            'default' => [
                'host' => 'magento-docker_db_1',
                'dbname' => 'netpower',
                'username' => 'root',
                'password' => '1',
                'active' => '1'
            ]
```
**magento-docker_db_1** is mysql container name 
## [Define build](https://docs.docker.com/compose/compose-file/)
Images are defined in docker-compose.yml with the coresponding format with [build](https://docs.docker.com/compose/compose-file/#build), [name](https://docs.docker.com/compose/compose-file/#credential_spec), [network](https://github.com/docker/labs/blob/master/networking/README.md), etc:
```php
name:
  build: directory/available image
  port:
  - port
  volumes:
  - name: path/to/dir
  networks:
  - docker docker container network
```
Example
```php
php:
    build: php #find the direcotory php 
    volumes:
      - ./source:/var/www/html
    networks:
      - appnet

  nginx:
    build: nginx
    ports:
      - 8081:8081
    volumes:
      - ./source:/var/www/html
    networks:
      - appnet

  db:
    image: mysql:5.7 #pull the mysql|:5.7 img
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - 32011:3201
    environment:
       MYSQL_ROOT_PASSWORD: 122
       MYSQL_DATABASE: db
       MYSQL_USER: root
       MYSQL_PASSWORD: 122
    networks:
      - appnet
```
## [Dockerfile](https://docs.docker.com/engine/reference/builder/)
Basically, it's a script for docker to automatically build an image base on the instruoction in the file
#### Basic format
Contain command like [FROM](https://docs.docker.com/engine/reference/builder/#from), [RUN](https://docs.docker.com/engine/reference/builder/#run), [ADD](https://docs.docker.com/engine/reference/builder/#cmd), and [more](https://docs.docker.com/engine/reference/builder/)
```php
FROM [--platform=<platform>] <image> [AS <name>]
# Pulling image from Public Repositories.
RUN <command>
# Or
RUN ["executable", "param1", "param2"]
#  use a \ (backslash) to continue a single RUN
```
#### A sample centos7-php72-composer build 
```php
FROM centos:7.7.1908

RUN yum -y --setopt=tsflags=nodocs update && \
    yum -y --setopt=tsflags=nodocs --nogpgcheck install epel-release && \
    yum -y --setopt=tsflags=nodocs --nogpgcheck install https://centos7.iuscommunity.org/ius-release.rpm && \
    yum -y --setopt=tsflags=nodocs --nogpgcheck install php72u-cli \
        php72u-fpm \
        php72u-bcmath \
        php72u-gd \
        php72u-intl \
        php72u-json \
        php72u-ldap  \
        php72u-mbstring \
        php72u-mcrypt \
        php72u-opcache \
        php72u-pdo \
        php72u-pear  \
        php72u-pecl-apcu \
        php72u-pecl-imagick \
        php72u-pecl-redis \
        php72u-pecl-xdebug  \
        php72u-pgsql \
        php72u-mysqlnd \
        php72u-soap \
        php72u-tidy \
        php72u-xml \
        php72u-xmlrpc && \
        yum clean all

RUN rm /etc/php-fpm.d/www.conf
ADD php72.conf /etc/php-fpm.d/
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

RUN echo 'memory_limit = 2G\n\
max_execution_time = 1800\n\
zlib.output_compression = On' >> /usr/local/etc/php.ini

WORKDIR /var/www/html
```
## Import sql databe to docker container
1. Build and start up your docker container
2. Run below comand
```php
docker exec -i [Mysql_container_name] mysql -u [username] -p [db_name] < [sql file]
```
