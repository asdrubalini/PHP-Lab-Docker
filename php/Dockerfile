FROM php:8.0.2-fpm

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
RUN docker-php-ext-install gd

RUN docker-php-ext-configure imap \
    --with-kerberos \
    --with-imap-ssl
RUN docker-php-ext-install imap

RUN docker-php-ext-configure zip
RUN docker-php-ext-install zip

RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl

RUN docker-php-ext-install exif
RUN docker-php-ext-install fileinfo

RUN docker-php-ext-install pdo pdo_pgsql

RUN pecl install xdebug

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ENV COMPOSER_ALLOW_SUPERUSER 1

RUN useradd -ms /bin/bash dev

RUN chown -R dev:dev /var/www/html/

ENV TZ=Europe/Rome
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN curl -fsSL https://deb.nodesource.com/setup_15.x | bash -
RUN apt install nodejs -y
RUN npm install -g npm

USER dev
WORKDIR /home/dev/

WORKDIR /var/www/html/
