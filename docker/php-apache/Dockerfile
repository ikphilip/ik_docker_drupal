
FROM php:7.2-apache

# install the PHP extensions we need
RUN set -ex; \
  \
  if command -v a2enmod; then \
    a2enmod rewrite; \
  fi; \
  \
  savedAptMark="$(apt-mark showmanual)"; \
  \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    libjpeg-dev \
    libpng-dev \
    libpq-dev \
  ; \
  \
  docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
  docker-php-ext-install -j "$(nproc)" \
    gd \
    opcache \
    pdo_mysql \
    pdo_pgsql \
    zip \
  ; \
  \
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
  apt-mark auto '.*' > /dev/null; \
  apt-mark manual $savedAptMark; \
  ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
    | awk '/=>/ { print $3 }' \
    | sort -u \
    | xargs -r dpkg-query -S \
    | cut -d: -f1 \
    | sort -u \
    | xargs -rt apt-mark manual; \
  \
  apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
  rm -rf /var/lib/apt/lists/*

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=60'; \
    echo 'opcache.fast_shutdown=1'; \
    echo 'opcache.enable_cli=1'; \
  } > /usr/local/etc/php/conf.d/opcache-recommended.ini

WORKDIR /var/www/html

#Generate Keys
RUN apt-get update && \
    apt-get install -y openssl && \
    openssl genrsa -out server.pass.key 2048 && \
    openssl rsa -passin pass:x -in server.pass.key -out server.key && \
    rm server.pass.key && \
    openssl req -new -key server.key -out server.csr \
        -subj "/C=US/ST=North Carolina/L=Charlotte/O=Interactive Knowledge/OU=Development/CN=interactiveknowledge.com" && \
    openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt;

#Apache Conf
ADD 001-drupal.conf /etc/apache2/sites-available/001-drupal.conf
ADD drupal-ssl.conf /etc/apache2/sites-available/drupal-ssl.conf
RUN mv server.crt /etc/ssl/certs/server.crt;
RUN mv server.key /etc/ssl/private/server.key;

ARG url
RUN { \
    echo ''; \
    echo 'export DOCKER_SERVER_NAME='$url; \
  } >> /etc/apache2/envvars

#Set Default Localhost
RUN touch /var/www/html/index.html;
RUN echo '<h1>Docker Localhost</h1>' >> /var/www/html/index.html;
RUN touch /var/www/html/phpinfo.php;
RUN echo '<?php phpinfo();' >> /var/www/html/phpinfo.php;

#Restart apache
RUN a2ensite 001-drupal.conf
RUN a2enmod ssl
RUN a2ensite drupal-ssl.conf
RUN service apache2 restart

#Change to Drupal directory
WORKDIR /var/www/drupal