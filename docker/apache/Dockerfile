ARG PHP_VERSION=7.4
FROM php:${PHP_VERSION}-apache

ENV SOURCE_NAME=biz_match_app

RUN apt-get update && apt-get install -y p7zip \
    p7zip-full \
    unace \
    zip \
    unzip \
    xz-utils \
    sharutils \
    uudeview \
    mpack \
    arj \
    cabextract \
    file-roller \
    && rm -rf /var/lib/apt/lists/*

# SSL
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -subj "/C=AT/ST=Vienna/L=Vienna/O=Security/OU=Development/CN=example.com"
RUN docker-php-ext-install pdo pdo_mysql
RUN a2enmod rewrite
RUN a2ensite default-ssl
RUN a2enmod ssl

RUN mkdir -p /etc/apache2/ssl
COPY /ssl /etc/apache2/ssl
COPY ./php/php.ini /usr/local/etc/php/

RUN apt-get update &&  apt-get install -y libmcrypt-dev
RUN pecl install mcrypt-1.0.4 && docker-php-ext-enable mcrypt

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p /var/www/html/${SOURCE_NAME} && \
    chown -R www-data.www-data /var/www/html/${SOURCE_NAME} && \
    chmod -R 755 /var/www/html/${SOURCE_NAME}

RUN apt-get update && apt-get install -y nodejs

EXPOSE 80
EXPOSE 443

# # Install node_modules
# RUN apt npm install

# # run composer
# RUN apt composer update && \
#     apt composer install