#FROM centos:7.7.1908
#RUN yum -y upgrade\
#    &&yum -y install php-cli php-zip wget unzip curl
#
#RUN echo "cgi.fix_pathinfo = 0;" >> /etc/php.ini
#
#RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer && chmod +x /usr/local/bin/composer
#
#VOLUME ["/var/www/html", "/var/log"]

#
#RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
#
#RUN echo 'memory_limit = 2G\n\
#max_execution_time = 1800\n\
#zlib.output_compression = On' >> /usr/local/etc/php/php.ini
#
#WORKDIR /var/www/html


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