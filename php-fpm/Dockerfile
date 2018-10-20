FROM php:7-fpm

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
    	&& docker-php-ext-configure gd --with-png-dir=/usr/include --with-jpeg-dir=/usr/include \
    	&& docker-php-ext-install gd \
        && docker-php-ext-install pdo_mysql

COPY symfony.pool.conf  /usr/local/etc/

RUN usermod -u 1000 www-data

EXPOSE 9000
CMD ["php-fpm", "-F"]
