FROM php:7.4-apache

RUN apt-get update && apt-get install -y \
        curl \
        wget \
        git \
        libfreetype6-dev \
        libonig-dev \
        libpq-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        libzip-dev \
        libxml2-dev \
    && pecl install mcrypt-1.0.3 \
    && docker-php-ext-install -j$(nproc) iconv mbstring mysqli pdo_mysql zip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd sockets \
    && docker-php-ext-enable mcrypt \
    && docker-php-ext-install exif soap

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Добавим свой php.ini, можем в нем определять свои значения конфига
ADD ./php.ini /usr/local/etc/php/conf.d/40-custom.ini

RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -subj "/C=RU/ST=Ulyanovsk/L=Ulyanovsk/O=My Organisation/OU=Development Departament/CN=localhost"

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf && a2enmod rewrite && \
    a2ensite default-ssl && a2enmod ssl && \
    service apache2 restart

RUN echo "RewriteEngine on" > /var/www/html/.htaccess

ADD ./000-default.conf /etc/apache2/sites-available/000-default.conf

RUN service apache2 restart

# Указываем рабочую директорию для PHP
WORKDIR /app
