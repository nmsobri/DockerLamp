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
    apt-get --no-install-recommends -y -qq install apt-utils build-essential libpq-dev libzip-dev \
    libsqlite3-dev libcurl4-openssl-dev pkg-config libssl-dev libicu-dev libjpeg62-turbo-dev libpng-dev \
    libwebp-dev libonig-dev libfreetype6-dev && rm -rf /var/lib/apt/lists/*

# Install php Extension
# Configure apache and remove default apache file and do cleanup
RUN set -eux; \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp; \
    docker-php-ext-install -j$(nproc) intl pdo pdo_mysql pdo_pgsql pdo_sqlite \
    pgsql mysqli curl bcmath tokenizer zip mbstring gettext calendar exif gd && \
    pecl install redis && docker-php-ext-enable redis && \
    pecl install mongodb && docker-php-ext-enable mongodb && \
    pecl install xdebug && docker-php-ext-enable xdebug && \
    sed -i "s:DocumentRoot /var/www/html:DocumentRoot /var/www/html/public:g" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "/#Include /a \\\tDirectoryIndex index.php" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "s/AllowOverride None/AllowOverride All/g" /etc/apache2/apache2.conf && a2enmod rewrite && \
    echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/php.ini && \
    rm -f /var/www/html/index.html && rm -rf /usr/src/* && apachectl restart
