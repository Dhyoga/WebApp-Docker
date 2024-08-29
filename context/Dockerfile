FROM php:8.1-fpm

RUN apt-get update && \
    apt-get install -y \
        zip \
        unzip \
        libzip-dev \
        curl \
        gnupg \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        git \
        nginx \
        && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd zip mysqli pdo pdo_mysql && \
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash && \
    curl https://getcomposer.org/installer > /tmp/composer-setup.php && \
    php /tmp/composer-setup.php --filename composer --install-dir /usr/local/bin && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm /tmp/composer-setup.php

ENV NVM_DIR /root/.nvm
ENV NODE_VERSION 18
RUN . "$NVM_DIR/nvm.sh" && nvm install $NODE_VERSION && nvm use $NODE_VERSION && nvm alias default $NODE_VERSION && npm install -g npm@6

COPY nginx.conf /etc/nginx/sites-available/default

COPY . /var/www/html/

WORKDIR /var/www/html

RUN COMPOSER_ALLOW_SUPERUSER=1 composer require ichtrojan/laravel-otp && \
    COMPOSER_ALLOW_SUPERUSER=1 composer install --no-interaction

RUN . "$NVM_DIR/nvm.sh" && npm install gsap && npm install --userconfig .npmrc && npm install -g cross-env && npm run build

COPY docker-php-entrypoint /usr/local/bin/docker-php-entrypoint
RUN chmod +x /usr/local/bin/docker-php-entrypoint

