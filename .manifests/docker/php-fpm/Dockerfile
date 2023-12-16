###########################################################
# Dependendo da sua arquiterura, mude aqui
# arm64v8 é para MacOS com arquitetura arm64 tipo os M1
###########################################################

#FROM arm64v8/php:8.1.9-fpm-alpine AS builder
FROM php:8.1.9-fpm-alpine AS builder

RUN apk update
RUN apk add --no-cache $PHPIZE_DEPS \
        linux-headers \
        bash \
        shadow \
        libpng \
        zlib-dev \
        libzip-dev \
        zip \
        libpng-dev \
        libjpeg-turbo-dev \
        libwebp-dev \
        libxpm-dev \
        libxml2-dev \
        mariadb-connector-c \
        mariadb-client \
        sqlite \
    && docker-php-ext-install \
        gd \
        mysqli \
        pdo_mysql \
        soap \
        bcmath \
        zip \
    && apk del \
        libpng-dev \
        libjpeg-turbo-dev \
        libwebp-dev \
        zlib-dev \
        libxpm-dev \
        libxml2-dev

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

RUN pecl install excimer \
    && docker-php-ext-enable excimer

RUN touch /home/www-data/.bashrc | echo "PS1='\w\$ '" >> /home/www-data/.bashrc

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

##### LOCAL DEVELOPMENT
FROM builder

WORKDIR /var/www

RUN cp "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

COPY confs/www.conf /usr/local/etc/php-fpm.d/zz-custom.conf

RUN usermod -u 1000 www-data
RUN chown -R www-data:www-data /var/www
USER www-data

#RUN ln -s public html

EXPOSE 9000
CMD [ "php-fpm" ]
