FROM php:8.0.5-fpm-alpine

RUN apk update \
    && apk add --no-cache git vim bash autoconf openssh-client \
    && apk add --no-cache --virtual .build-deps \
    gcc \
    g++ \
    make \
    openssl-dev \
    icu-dev

RUN apk add --no-cache freetype-dev libwebp-dev libjpeg-turbo-dev \
    && docker-php-ext-configure gd \
    --with-webp=/usr/include/ \
    --with-freetype=/usr/include/ \
    --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-enable gd

RUN apk add --no-cache libzip-dev zip \
    && docker-php-ext-configure zip \
    && docker-php-ext-install -j$(nproc) zip \
    && docker-php-ext-enable zip

RUN docker-php-ext-install exif \
    && docker-php-ext-install -j$(nproc) fileinfo

RUN docker-php-ext-install -j$(nproc) pdo pdo_mysql

RUN docker-php-ext-install -j$(nproc) bcmath

RUN apk add --no-cache gmp-dev \
    && docker-php-ext-install -j$(nproc) gmp

RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

# Cleanup libs
RUN apk del --no-cache .build-deps

# Get latest Composer
RUN curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php \
    && php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Install yarn
RUN apk add --update yarn

# Install NPM
RUN apk add --update nodejs npm

RUN addgroup -g 1000 www \
    && adduser -S -u 1000 -G www -s /bin/bash www

RUN chown -R www:www /var/www

USER www

WORKDIR /var/www/
