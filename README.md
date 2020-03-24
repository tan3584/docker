# Docker
A basic docker isntallation and knowledges
## Requirements
#### Window
Install [Docker](https://docs.docker.com/docker-for-windows/install/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
#### Macos
Install [Docker](https://docs.docker.com/docker-for-mac/install/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
#### Linux
Install [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/) and [Docker-compose](https://docs.docker.com/compose/install/#install-compose).
Follow the instruction on the docker [docs](https://docs.docker.com/install/) with your coresponding platform  
For [compose](https://docs.docker.com/compose/install/)
## Define images
Images are defined in docker-compose.yml with the coresponding format:
```php
name:
  build: directory/available image
  port:
  - port
  volumes:
  - name: path/to/dir
  networks:
  - docker docker container [network](https://github.com/docker/labs/blob/master/networking/README.md)
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
