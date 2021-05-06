FROM php:8.0.5-fpm

ADD php.ini /usr/local/etc/php/conf.d/php.ini

RUN apt-get update -y
RUN apt-get install -y \
    git \
    libzip-dev \
    libc-client-dev \
    libkrb5-dev \
    libpng-dev \
    libjpeg-dev \
    libwebp-dev \
    libfreetype6-dev \
    libkrb5-dev \
    libicu-dev \
    zlib1g-dev \
    zip \
    build-essential \
    gnupg2 \
    libpq-dev \
    libpq5 \
    libz-dev \
    vim

RUN docker-php-ext-configure gd \
    --with-webp=/usr/include/ \
    --with-freetype=/usr/include/ \
    --with-jpeg=/usr/include/
RUN docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-configure imap \
    --with-kerberos \
    --with-imap-ssl
RUN docker-php-ext-install -j$(nproc) imap

RUN docker-php-ext-configure zip
RUN docker-php-ext-install -j$(nproc) zip

RUN docker-php-ext-configure intl
RUN docker-php-ext-install -j$(nproc) intl

RUN docker-php-ext-install exif
RUN docker-php-ext-install -j$(nproc) fileinfo

RUN docker-php-ext-install -j$(nproc) pdo pdo_pgsql

RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

ENV EXT_REDIS_VERSION=5.3.4 EXT_IGBINARY_VERSION=3.2.2

RUN docker-php-source extract \
    # igbinary
    && mkdir -p /usr/src/php/ext/igbinary \
    &&  curl -fsSL https://github.com/igbinary/igbinary/archive/$EXT_IGBINARY_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/igbinary --strip 1 \
    && docker-php-ext-install -j$(nproc) igbinary \
    # redis
    && mkdir -p /usr/src/php/ext/redis \
    && curl -fsSL https://github.com/phpredis/phpredis/archive/$EXT_REDIS_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && docker-php-ext-configure redis --enable-redis-igbinary \
    && docker-php-ext-install -j$(nproc) redis \
    # cleanup
    && docker-php-source delete

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ENV COMPOSER_ALLOW_SUPERUSER 1

RUN useradd -ms /bin/bash dev

RUN chown -R dev:dev /var/www/html/

ENV TZ=Europe/Rome
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN curl -fsSL https://deb.nodesource.com/setup_15.x | bash -
RUN apt install nodejs -y
RUN npm install -g npm

# Cleanup apt
RUN rm -rf /var/lib/apt/lists/*

USER dev
WORKDIR /home/dev/

WORKDIR /var/www/html/
