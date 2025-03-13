# Verwende das offizielle PHP-Apache-Image
FROM php:8.3.0-apache

RUN mkdir -p /usr/share/man/man1

# Setze das Apache DocumentRoot auf das "public"-Verzeichnis
ENV APACHE_DOCUMENT_ROOT=/app/public
ARG SMTP_USER
ARG SMTP_PASSWORD
ENV SMTP_USER=${SMTP_USER}
ENV SMTP_PASSWORD=${SMTP_PASSWORD}
# Hole den neuesten Commit-Hash aus dem Repository
ARG COMMIT_HASH
ENV COMMIT_HASH=${COMMIT_HASH}

# Install locales and set the default locale
RUN apt-get update && apt-get install -y locales \
    && echo "de_DE.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen de_DE.UTF-8 \
    && update-locale LANG=de_DE.UTF-8

RUN apt-get update \
    && apt-get install -y \
    pkg-config \
    software-properties-common \
    supervisor \
    nano \
    wget \
    net-tools \
    lsof \
    sqlite3 \
    libsqlite3-dev \
    libicu-dev \
    git \
    openssl \
    curl \
    zip \
    unzip \
    npm \
    mariadb-client \
    msmtp  \
    msmtp-mta

RUN npm install -g sass
RUN npm install -D tailwindcss postcss autoprefixer sass

RUN pecl install redis

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN docker-php-ext-configure pcntl --enable-pcntl \
    && docker-php-ext-install pcntl

## Installiere zusätzliche PHP-Erweiterungen (falls benötigt, z. B. mysqli für Datenbankverbindungen)
RUN docker-php-ext-install -j "$(nproc)" mysqli pdo pdo_mysql pdo_sqlite opcache intl

RUN docker-php-ext-enable redis

RUN a2enmod rewrite

# Copy additional php.ini
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS="0"
ENV PHP_OPCACHE_ENABLE="0"
ENV PHP_OPCACHE_ENABLE_CLI="0"

# Arbeite in /app
WORKDIR /app

# Kopiere die Projektdateien in das Verzeichnis
COPY ./app ./

## Passe die Apache-Konfiguration an, sodass das "public"-Verzeichnis verwendet wird
RUN sed -ri -e "s!/var/www/html!${APACHE_DOCUMENT_ROOT}!g" /etc/apache2/sites-available/*.conf && \
    sed -ri -e "s!/var/www/!${APACHE_DOCUMENT_ROOT}!g" /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN mkdir -p /app && \
    chown www-data:www-data /app && \
    chmod 750 /app

# Schreibe den Commit-Hash in die index.php
RUN echo "<?php define('COMMIT_HASH', '${COMMIT_HASH}');" > /public/version.php

RUN  echo 'export PATH="$HOME/.config/composer/vendor/bin:$PATH"' >> ~/.bashrc

# Installiere msmtp und setze Konfiguration
RUN echo "account default" > /etc/msmtprc && \
    echo "host server.brtlbyntwrk.de" >> /etc/msmtprc && \
    echo "port 465" >> /etc/msmtprc && \
    echo "from no_reply@brtlbyntwrk.de" >> /etc/msmtprc && \
    echo "set_from_header on" >> /etc/msmtprc && \
    echo "tls on" >> /etc/msmtprc && \
    echo "tls_starttls off" >> /etc/msmtprc && \
    echo "auth on" >> /etc/msmtprc && \
    echo "user no_reply@brtlbyntwrk.de" >> /etc/msmtprc && \
    echo "password $SMTP_PASSWORD" >> /etc/msmtprc && \
	echo 'sendmail_path = "/usr/bin/msmtp -t"' > /usr/local/etc/php/conf.d/mail.ini

## Starte Apache, wenn der Container hochfährt
CMD ["apache2-foreground"]
