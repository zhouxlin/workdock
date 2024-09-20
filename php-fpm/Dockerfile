ARG PHP_VERSION
FROM ${IMAGE_ARCH}php:${PHP_VERSION}-fpm


RUN apt-get update && apt-get install -y \
    libzip-dev \
    zip \ 
    # && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install pdo_mysql zip 

# install composer
COPY --from=composer/composer:latest-bin /composer /usr/local/bin/composer

CMD ["php-fpm"]
WORKDIR /var/www
