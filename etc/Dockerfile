FROM php:8.4-fpm-alpine

RUN apk add --update --no-cache \
    bash \
    build-base \
    autoconf \
    libzip-dev \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    oniguruma-dev \
    libxml2-dev \
    sqlite-dev \
    mysql-client \
    git \
    openssh-client \
    imagemagick-dev \
    libwebp-dev \
    icu-dev \ 
    nodejs \
    npm

COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

ENV COMPOSER_ALLOW_SUPERUSER=1

RUN docker-php-ext-install pdo pdo_mysql gd exif bcmath mbstring zip intl
RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp

RUN pecl install imagick \
    && docker-php-ext-enable imagick

WORKDIR /var/www/html

COPY web/composer.json web/composer.lock web/artisan web/.env.example ./
COPY web/bootstrap ./bootstrap
COPY web/config ./config
COPY web/app ./app
COPY web/routes ./routes

RUN mkdir -p storage/framework/sessions storage/framework/views storage/framework/cache bootstrap/cache \
    && chmod -R 777 storage bootstrap/cache

RUN composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader \
    && composer clear-cache \
    && composer dump-autoload

COPY web/ .

COPY etc/php/php.ini /usr/local/etc/php/php.ini

RUN chmod a+x /usr/local/bin/*

EXPOSE 9000
