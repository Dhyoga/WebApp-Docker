FROM php:apache
COPY sites-available /etc/apache2/sites-available
COPY --chmod=775 docker-php-entrypoint /usr/local/bin/docker-php-entrypoint
RUN apt-get update && apt-get install -y zip unzip libzip-dev && curl https://getcomposer.org/installer > /tmp/composer-setup.php && php /tmp/composer-setup.php --filename composer --install-dir /usr/local/bin && docker-php-ext-install zip && apt-get clean && rm -rf /var/lib/apt/lists/* && rm /tmp/composer-setup.php
