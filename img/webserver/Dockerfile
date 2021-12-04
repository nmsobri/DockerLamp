FROM php:8.0-apache

# Update sources and install locale
RUN apt-get update --fix-missing
RUN apt-get upgrade -y

# Install important libraries
RUN apt-get -y install --fix-missing apt-utils build-essential libpq-dev libzip-dev libcurl4-openssl-dev pkg-config libssl-dev

# Install useful tools
RUN apt-get -y install --fix-missing vim wget dialog git zip unzip

# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install & enable mysqli
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli

# Install & enable pgsql
RUN docker-php-ext-install pgsql && docker-php-ext-enable pgsql

# Install & enable mongodb
RUN pecl install mongodb && docker-php-ext-enable mongodb

# Install & enable bcmath
RUN docker-php-ext-install bcmath && docker-php-ext-enable bcmath

# Install & enable zip
RUN docker-php-ext-install zip && docker-php-ext-enable zip

# Install & enable redis
RUN pecl install redis && docker-php-ext-enable redis

# Install & enable pdo
RUN docker-php-ext-install pdo pdo_mysql pdo_pgsql

# Install & enable xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug && apachectl restart

# Configure mailhog for php
RUN apt-get update && apt-get install --no-install-recommends --assume-yes --quiet ca-certificates curl git && rm -rf /var/lib/apt/lists/*
RUN curl -Lsf 'https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -
ENV PATH /usr/local/go/bin:$PATH
RUN go get github.com/mailhog/mhsendmail
RUN cp /root/go/bin/mhsendmail /usr/bin/mhsendmail
RUN echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/php.ini

# Chmod system/data/, composer intall, create .env file and remove default apache file
RUN rm -f /var/www/html/index.html

# Configure apache
RUN sed -i "s:DocumentRoot /var/www/html:DocumentRoot /var/www/html/public:g" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "/#Include /a \\\tDirectoryIndex index.php" /etc/apache2/sites-enabled/000-default.conf && \
    sed -i "s/AllowOverride None/AllowOverride All/g" /etc/apache2/apache2.conf && a2enmod rewrite
