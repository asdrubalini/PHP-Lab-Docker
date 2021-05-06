FROM php:8.0.5-fpm-alpine

ENV EXT_REDIS_VERSION=5.3.4 EXT_IGBINARY_VERSION=3.2.2

ADD php.ini /usr/local/etc/php/conf.d/php.ini

RUN apk update \
    && apk add --no-cache git vim \
    && apk add --no-cache --virtual .build-deps \
    gcc \
    g++ \
    make \
    libwebp-dev \
    freetype-dev \
    jpeg-dev \
    imap-dev \
    openssl-dev \
    libzip-dev \
    icu-dev \
    autoconf \
    && apk add --no-cache postgresql-dev

RUN docker-php-ext-configure gd \
    --with-webp=/usr/include/ \
    --with-freetype=/usr/include/ \
    --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-configure zip \
    && docker-php-ext-install -j$(nproc) zip

RUN docker-php-ext-configure intl \
    && docker-php-ext-install -j$(nproc) intl

RUN docker-php-ext-install exif \
    && docker-php-ext-install -j$(nproc) fileinfo

RUN docker-php-ext-install -j$(nproc) pdo pdo_pgsql

RUN pecl install xdebug

RUN docker-php-source extract \
    # igbinary
    && mkdir -p /usr/src/php/ext/igbinary \
    && curl -fsSL https://github.com/igbinary/igbinary/archive/$EXT_IGBINARY_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/igbinary --strip 1 \
    && docker-php-ext-install -j$(nproc) igbinary \
    # redis
    && mkdir -p /usr/src/php/ext/redis \
    && curl -fsSL https://github.com/phpredis/phpredis/archive/$EXT_REDIS_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && docker-php-ext-configure redis --enable-redis-igbinary \
    && docker-php-ext-install -j$(nproc) redis \
    # cleanup
    && docker-php-source delete

# Cleanup libs
RUN apk del --no-cache .build-deps

RUN adduser -S dev \
    && chown -R dev /var/www/html/

USER dev
WORKDIR /var/www/html/
