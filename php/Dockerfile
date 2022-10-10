ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

#设置时区和环境变量
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Chongqing
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
#更换阿里云 apt-get镜像源
RUN printf "deb https://mirrors.aliyun.com/debian/ bullseye main non-free contrib\n \
deb-src https://mirrors.aliyun.com/debian/ bullseye main non-free contrib\n \
deb https://mirrors.aliyun.com/debian-security/ bullseye-security main\n \
deb-src https://mirrors.aliyun.com/debian-security/ bullseye-security main\n \
deb https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib\n \
deb-src https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib\n \
deb https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib\n \
deb-src https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib" > /etc/apt/sources.list

##安装基础扩展
RUN apt-get update && apt-get install -y \
		libfreetype6-dev \
        libjpeg62-turbo-dev \
		libpng-dev \
        libzip-dev \
        zip \
        unzip \
        git \
        vim \
        iputils-ping \
        net-tools \
        curl \
        python \
        wget

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
      && docker-php-ext-install gd bcmath pdo_mysql opcache zip pcntl

##安装pecl扩展
RUN pecl install redis && docker-php-ext-enable redis
RUN pecl install swoole && docker-php-ext-enable swoole
#grpc protobuf扩展
#RUN pecl install protobuf && docker-php-ext-enable protobuf

#php配置文件
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

#安装php composer
RUN php -r "readfile('https://getcomposer.org/installer');" | php
RUN mv composer.phar /usr/local/bin/composer
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

##安装node
ARG NPM_VERSION
RUN cd /tmp/ && wget https://npm.taobao.org/mirrors/node/v${NPM_VERSION}/node-v${NPM_VERSION}-linux-x64.tar.xz && \
    tar -xvJf node-v${NPM_VERSION}-linux-x64.tar.xz && \
    ln -s /tmp/node-v${NPM_VERSION}-linux-x64/bin/node  /usr/local/bin/ && \
    ln -s /tmp/node-v${NPM_VERSION}-linux-x64/bin/npm  /usr/local/bin/
RUN npm config set registry https://registry.npm.taobao.org --global
RUN npm config set disturl https://npm.taobao.org/dist --global
RUN npm install --global node-gyp


# Set Fpm Max Value
RUN cd /usr/local/etc/php-fpm.d/ && \
    sed  -i '/^pm.max_children/s/[0-9]\+/50/g' www.conf && \
    sed  -i '/^pm.start_servers/s/[0-9]\+/10/g' www.conf && \
    sed  -i '/^pm.min_spare_servers/s/[0-9]\+/10/g' www.conf && \
    sed  -i '/^pm.max_spare_servers/s/[0-9]\+/50/g' www.conf && \
    echo 'php_admin_value[memory_limit] = 500M' >> www.conf

RUN echo 'swoole.use_shortname=Off' >> /usr/local/etc/php/php.ini

WORKDIR /opt/www