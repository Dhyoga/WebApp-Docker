FROM php:apache

# Salin konfigurasi situs ke dalam kontainer
COPY sites-available /etc/apache2/sites-available

# Salin entrypoint script dan berikan izin eksekusi
COPY --chmod=775 docker-php-entrypoint /usr/local/bin/docker-php-entrypoint

# Instalasi dependencies dan Composer
RUN apt-get update && \
    apt-get install -y zip unzip libzip-dev && \
    curl https://getcomposer.org/installer > /tmp/composer-setup.php && \
    php /tmp/composer-setup.php --filename composer --install-dir /usr/local/bin && \
    docker-php-ext-install zip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm /tmp/composer-setup.php

# Salin folder app dari luar konteks build Docker
COPY ../app /var/www/html
RUN composer install --working-dir=/var/www/html

# Menyalin dan memberikan izin pada direktori yang diperlukan
RUN chmod a+w -R /var/www/html/storage/logs && \
    chmod a+w -R /var/www/html/storage/framework && \
    chmod a+w -R /var/www/html/storage/app
