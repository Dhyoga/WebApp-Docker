FROM php:7.4.33-apache-buster

# Instalasi dependencies, nvm, Node.js, npm, Git, dan Composer
RUN apt-get update && \
    apt-get install -y zip unzip libzip-dev curl gnupg libfreetype6-dev libjpeg62-turbo-dev libpng-dev git && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd && \
    # Instalasi nvm
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash && \
    # Instalasi Composer
    curl https://getcomposer.org/installer > /tmp/composer-setup.php && \
    php /tmp/composer-setup.php --filename composer --install-dir /usr/local/bin && \
    docker-php-ext-install zip mysqli pdo pdo_mysql && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm /tmp/composer-setup.php

# Setup nvm dan instalasi Node.js serta npm
ENV NVM_DIR /root/.nvm
ENV NODE_VERSION 12
RUN . "$NVM_DIR/nvm.sh" && nvm install $NODE_VERSION && nvm use $NODE_VERSION && nvm alias default $NODE_VERSION && npm install -g npm@6

# Salin konfigurasi situs ke dalam kontainer
COPY sites-available /etc/apache2/sites-available

# Salin entrypoint script dan berikan izin eksekusi
COPY --chmod=775 docker-php-entrypoint /usr/local/bin/docker-php-entrypoint

# Aktifkan modul mod_rewrite Apache
RUN a2enmod rewrite

# Salin package.json dan package-lock.json untuk caching npm install
COPY app /var/www/html/

# Instalasi npm dependencies
RUN . "$NVM_DIR/nvm.sh" && cd /var/www/html && npm i && npm install -g cross-env

# Jalankan composer install dan update dengan plugin diizinkan
RUN COMPOSER_ALLOW_SUPERUSER=1 composer install --working-dir=/var/www/html && \
    COMPOSER_ALLOW_SUPERUSER=1 composer update --working-dir=/var/www/html

# Build production assets
RUN . "$NVM_DIR/nvm.sh" && cd /var/www/html && npm run prod

# Menyalin dan memberikan izin pada direktori yang diperlukan
RUN chmod a+w -R /var/www/html/storage/logs && \
    chmod a+w -R /var/www/html/storage/framework && \
    chmod a+w -R /var/www/html/storage/app
