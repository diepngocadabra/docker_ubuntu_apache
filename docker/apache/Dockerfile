ARG PHP_VERSION=7.4
FROM php:${PHP_VERSION}-apache

ENV SOURCE_NAME=biz_match_app
ENV HOSTNAME=localhost

COPY ./ssl/certs/ /etc/ssl/certs/
COPY ./ssl/private/ /etc/ssl/private/
COPY ./conf/sites-enabled/000-default.conf /etc/apache2/sites-available/000-default.conf

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

RUN docker-php-ext-install pdo pdo_mysql

# SSL
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/local.key -out /etc/ssl/certs/local.pem \
    -subj "/C=AT/ST=Vienna/L=Vienna/O=Security/OU=Development/CN=${HOSTNAME}"
RUN openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

RUN a2ensite default-ssl
RUN a2enmod ssl

RUN mkdir -p /etc/apache2/ssl
COPY /ssl /etc/apache2/ssl
COPY ./php/php.ini /usr/local/etc/php/

RUN apt-get update &&  apt-get install -y libmcrypt-dev
RUN pecl install mcrypt-1.0.4 && docker-php-ext-enable mcrypt

RUN a2enmod rewrite
RUN a2enmod http2

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p /var/www/html/${SOURCE_NAME} && \
    chown -R www-data.www-data /var/www/html/${SOURCE_NAME} && \
    chmod -R 755 /var/www/html/${SOURCE_NAME}

RUN apt-get update && apt-get install -y nodejs

EXPOSE 80
EXPOSE 443
