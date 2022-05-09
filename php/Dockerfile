ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

ENV SONAR_VERSION "4.6.0.2311"

RUN apt-get update && apt-get install -y libmcrypt-dev git curl libzip-dev libpng-dev zip unzip vim openssl \
    libmagickwand-dev --no-install-recommends ssh\
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && docker-php-ext-install gd mbstring pdo pdo_mysql mysqli exif mbstring zip \
	&& apt-get install -y wget

RUN \
    apt-get update && \
    apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

RUN pecl install redis && docker-php-ext-enable redis

COPY config /usr/local/etc/php
# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#install php-cs-fixer
RUN curl -L https://cs.symfony.com/download/php-cs-fixer-v2.phar -o php-cs-fixer
RUN chmod a+x php-cs-fixer && \
	mv php-cs-fixer /usr/local/bin/php-cs-fixer

# Set permissions
ARG VOLUME_PATH
RUN echo ${VOLUME_PATH}
#RUN mkdir ${VOLUME_PATH}
RUN chown -R www-data:www-data ${VOLUME_PATH}

# Install Sonar scanners
RUN cd /tmp && \
    wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-$SONAR_VERSION-linux.zip && \
    unzip sonar-scanner-* && cd sonar-scanner-* && \
    mkdir -p /usr/local/sonar-scanner && mv ./* /usr/local/sonar-scanner/ && \
    ln -s /usr/local/sonar-scanner/bin/sonar-scanner /usr/local/bin/sonar-scanner && \
    chmod +x /usr/local/bin/sonar-scanner

COPY sonar-scanner.properties /usr/local/sonar-scanner/conf/

# Add docker alias for development
RUN echo 'alias sonar="/usr/local/bin/sonar-scanner"' >> ~/.bashrc
#fixer /path/to/file
RUN echo 'alias fixer="php-cs-fixer fix --show-progress=estimating --verbose"' >> ~/.bashrc
RUN echo 'alias fixer_debug="php-cs-fixer fix --show-progress=estimating --vvvv --profile"' >> ~/.bashrc
