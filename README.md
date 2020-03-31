# Docker
A basic docker isntallation and knowledges  
## Content
[Usage](#Usage)  
[Errors](#Errors-and-fix)  
[Knowledge](#Define-build)  
[Storage Issue](#Storage-issue)
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
## See container ip
```php
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id
```
# Storage issue
docker usually consum lots of disk space, when delete the container the consumed space still there, you can either expand the disk space or remove docker and reinstall it via **yum**/**apt** 
## Exspand disk space on vmware
Here a step by step how to extend a virtual disk in vmware
#### In Virtual Machine setting
1. Power off your vm
2. Navigate to the current vm **setting -> Hard disk -> Expand disk capacity**
#### Extend partition within a Virtual Machine
1. Switch to root user and exercute ```fdisk -l ```
```php fdisk -l 
Disk /dev/sda: 171.8 GB, 171798691840 bytes, 335544320 sectors
```
2. Using ```fdisk```, create a new partition on the ```/dev/sda``` device. Enter ```n```, to create a new partition:
```php
# fdisk /dev/sda
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Command (m for help): n
```
3. Now choose ```p``` to create a new primary partition
4. Choose your partition number. Since we already had ```/dev/sda1``` and ```/dev/sda2```, the logical number would be 3
```
Partition number (3,4, default 3): 3
```
5. Choose the first and last sectors for new partition, if you hit ENTER, then by default new partition will use all available disk space
6. Now follow the command to change the partition type
```php
Command (m for help): t
Partition number (1-3, default 3): 3
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```
7. Scan for newly created partition
```php
partprobe -s
/dev/sda: msdos partitions 1 2 3
```
#### Extend the Logical Volume with the new partition
1. Create the physical volume as a basis for your LVM. Please replace /dev/sda3 with the newly created partition.
```php
# pvcreate /dev/sda3
Physical volume "/dev/sda3" successfully created
```
2. Now find out how your Volume Group is called. In my case it's ```centos```
```php
--- Volume group ---
VG Name               centos
...
```
3. Extend that Volume Group by adding the newly created physical volume to it
```php
# vgextend centos /dev/sda3

Volume group "centos" successfully extended
```
4. Check the logical volumes available on system using command ```ls /dev/[Groupname]```, centos is Groupname in my case
To extend the logical volume ```root```, execute command:

```php
# lvextend /dev/cl/root /dev/sda3
Size of logical volume cl/root changed from 111.00 GiB (28415 extents) to 142.99 GiB (36606 extents).
Logical volume cl/root successfully resized.
```
5. All that remains now, is to resize the file system to the volume group. Execute ```xfs_growfs``` command as shown below (replace centos-root with the name of volume group on your system)
```php 
# xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root    isize=512    agcount=4, agsize=7274240 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=29096960, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=14207, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 29096960 to 37484544
```
6. Execute ```df -h``` to confirm that new disk size is available to the Virtual Machine
7. Restart your VM 
