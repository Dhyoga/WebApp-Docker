FROM php:7.4-apache

# Salin konfigurasi situs ke dalam kontainer
COPY sites-available /etc/apache2/sites-available

# Salin entrypoint script dan berikan izin eksekusi
COPY --chmod=775 docker-php-entrypoint /usr/local/bin/docker-php-entrypoint

# Instalasi dependencies, nvm, Node.js, npm, dan Composer
RUN apt-get update && \
    apt-get install -y zip unzip libzip-dev curl gnupg libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd && \
    # Instalasi nvm dan Node.js versi 12
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash && \
    export NVM_DIR="/root/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    nvm install 12 && \
    nvm use 12 && \
    nvm alias default 12 && \
    # Instalasi npm versi 6 (opsional, Node.js 12 biasanya sudah menyertakan npm versi 6)
    npm install -g npm@6 && \
    # Instalasi Composer
    curl https://getcomposer.org/installer > /tmp/composer-setup.php && \
    php /tmp/composer-setup.php --filename composer --install-dir /usr/local/bin && \
    docker-php-ext-install zip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm /tmp/composer-setup.php

# Aktifkan modul mod_rewrite Apache
RUN a2enmod rewrite

# Salin folder app dari luar konteks build Docker
COPY app /var/www/html

# Jalankan composer install dan update dengan plugin diizinkan
RUN COMPOSER_ALLOW_SUPERUSER=1 composer install --working-dir=/var/www/html && \
    COMPOSER_ALLOW_SUPERUSER=1 composer update --working-dir=/var/www/html

# Instalasi npm dependencies dan build production assets
RUN export NVM_DIR="/root/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    npm install -g cross-env && \
    cd /var/www/html && \
    npm install && \
    npm run prod

# Menyalin dan memberikan izin pada direktori yang diperlukan
RUN chmod a+w -R /var/www/html/storage/logs && \
    chmod a+w -R /var/www/html/storage/framework && \
    chmod a+w -R /var/www/html/storage/app