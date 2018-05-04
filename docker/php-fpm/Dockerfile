ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}-fpm

LABEL maintainer="Robson Tenório <robsontenorio@me.com>"

# BASIC
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    curl \
    libmemcached-dev \
    libz-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libssl-dev \
    libmcrypt-dev \
  && rm -rf /var/lib/apt/lists/*

 
# PDO_MYSQL extension
RUN docker-php-ext-install pdo_mysql 

# GD extension
RUN docker-php-ext-configure gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd 

# ZIP extension
RUN docker-php-ext-install zip 

# BCMATH extension
RUN docker-php-ext-install bcmath 

# MYSQLI extension
RUN docker-php-ext-install mysqli 
RUN docker-php-ext-install tokenizer

# INTL and requirements
RUN apt-get update -yqq && \
    apt-get install -y \
    zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl 

RUN apt-get install -y \ 
    jpegoptim optipng pngquant gifsicle

RUN apt-get install -y \ 
    libmagickwand-dev imagemagick && \
    pecl install imagick && \
    docker-php-ext-enable imagick 

# REDIS
RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis 

#OPCACHE
RUN docker-php-ext-install opcache 

COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./laravel.ini /usr/local/etc/php/conf.d
COPY ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

# GIT
RUN apt-get install -y git

# COMPOSER
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# NANO
RUN apt-get install -y nano

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

WORKDIR /var/www/api

CMD ["php-fpm"]

EXPOSE 9000
