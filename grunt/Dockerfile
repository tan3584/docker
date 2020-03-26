From ubuntu:18.04

RUN apt-get update \
&& apt-get install -y curl \
&& curl -sL https://deb.nodesource.com/setup_10.x | bash \
&& apt-get install -y nodejs \
&& apt-get install -y build-essential \
&& npm install -g grunt-cli

WORKDIR /var/www/html