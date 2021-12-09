FROM golang:buster AS builder
# Configure mailhog for php
RUN apt-get --no-install-recommends -y -qq install ca-certificates && go install github.com/mailhog/mhsendmail@latest && rm -rf /var/lib/apt/lists/*

FROM php:8.0-apache-buster
COPY --from=builder /go/bin/mhsendmail /usr/bin/mhsendmail

# Surpresses debconf complaints of trying to install apt packages interactively
# https://github.com/moby/moby/issues/4032#issuecomment-192327844
ARG DEBIAN_FRONTEND=noninteractive

# Update sources and install important tools / libraries
RUN apt-get --fix-missing -y -qq update && apt-get -y -qq upgrade && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    apt-get --no-install-recommends -y -qq install apt-utils build-essential libpq-dev libzip-dev libsqlite3-dev \
    libcurl4-openssl-dev pkg-config libssl-dev libicu-dev libonig-dev && rm -rf /var/lib/apt/lists/*

# Install php Extension
# Configure apache and remove default apache file and do cleanup
RUN docker-php-ext-install pdo && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install pdo_pgsql && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install pgsql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install curl && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install tokenizer && \
    docker-php-ext-install zip && \
    docker-php-ext-install -j$(nproc) intl && \
    docker-php-ext-install mbstring && \
    docker-php-ext-install gettext && \
    docker-php-ext-install calendar && \
    docker-php-ext-install exif && \
    pecl install redis && docker-php-ext-enable redis && \
    pecl install mongodb && docker-php-ext-enable mongodb && \
    pecl install xdebug && docker-php-ext-enable xdebug && \
    sed -i "s:DocumentRoot /var/www/html:DocumentRoot /var/www/html/public:g" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "/#Include /a \\\tDirectoryIndex index.php" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "s/AllowOverride None/AllowOverride All/g" /etc/apache2/apache2.conf && a2enmod rewrite && \
    echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/php.ini && \
    rm -f /var/www/html/index.html && rm -rf /usr/src/* && apachectl restart
