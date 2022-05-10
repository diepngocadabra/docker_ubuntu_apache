ARG PHP_VERSION=7.4
FROM php:${PHP_VERSION}-apache

ENV SOURCE_NAME=biz_match_app
ENV DOMAIN=local.biz-match.com
ENV EMAIL_ADDRESS=admin@gmail.com

COPY ./conf/sites-enabled/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY ./conf/vhost.conf /etc/apache2/sites-enabled/vhost.conf

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
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -subj "/C=AT/ST=Vienna/L=Vienna/O=Security/OU=Development/CN=example.com"
RUN a2enmod rewrite
RUN a2ensite default-ssl
RUN a2enmod ssl

RUN mkdir -p /etc/apache2/ssl
COPY /ssl /etc/apache2/ssl
COPY ./php/php.ini /usr/local/etc/php/

RUN apt-get update &&  apt-get install -y libmcrypt-dev
RUN pecl install mcrypt-1.0.4 && docker-php-ext-enable mcrypt



RUN apt update && apt install -y certbot python3-certbot-apache && \
    openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
RUN mkdir -p /var/lib/letsencrypt/.well-known && \
    chgrp www-data /var/lib/letsencrypt && \
    chmod g+s /var/lib/letsencrypt

RUN a2enmod ssl && \
    a2enmod headers

COPY ./conf/conf-available/letsencrypt.conf /etc/apache2/conf-available/letsencrypt.conf
COPY ./conf/conf-available/ssl-params.conf /etc/apache2/conf-available/ssl-params.conf

RUN a2enconf letsencrypt && \
    a2enconf ssl-params && \
    a2enmod http2

# RUN sudo systemctl reload apache2

RUN certbot certonly --agree-tos --email ${EMAIL_ADDRESS} --webroot -w /var/lib/letsencrypt/ -d ${DOMAIN} -d www.${DOMAIN}

COPY conf/sites-available/local.biz_match.com.conf /etc/apache2/sites-available/local.biz_match.com.conf
# RUN  systemctl reload apache2

RUN a2enmod headers

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