ARG PHP_VERSION=8.2
ARG NODE_VERSION=20

# ======================================================================================================================
#  BASE STAGE
# ======================================================================================================================
FROM php:${PHP_VERSION}-fpm-alpine AS base

ARG INSTALL_BZ2=false
ARG INSTALL_GD=false
ARG INSTALL_LDAP=false
ARG INSTALL_PGSQL=false
ARG INSTALL_REDIS=false

# Replace Alpine package repositories with Aliyun mirror to prevent network timeouts
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update && apk add --no-cache \
    git \
    curl \
    gnupg \
    ca-certificates \
    libpng-dev \
    oniguruma-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    icu-dev \
    freetype-dev \
    libjpeg-turbo-dev \
    openldap-dev \
    bzip2-dev \
    sqlite \
    sqlite-dev \
    mysql-client \
    vim \
    bash \
    linux-headers \
    $PHPIZE_DEPS \
    $( [ "$INSTALL_PGSQL" = "true" ] && echo "postgresql-dev" )

# Configure and install PHP extensions
RUN docker-php-ext-install \
    pdo_mysql \
    mysqli \
    zip \
    intl \
    bcmath \
    pcntl \
    opcache \
    && if [ "$INSTALL_BZ2" = "true" ]; then \
    docker-php-ext-install bz2; \
    fi \
    && if [ "$INSTALL_GD" = "true" ]; then \
    docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd; \
    fi \
    && if [ "$INSTALL_LDAP" = "true" ]; then \
    docker-php-ext-configure ldap \
    && docker-php-ext-install ldap; \
    fi \
    && if [ "$INSTALL_PGSQL" = "true" ]; then \
    docker-php-ext-install pdo_pgsql; \
    fi \
    && if [ "$INSTALL_REDIS" = "true" ]; then \
    pecl install redis \
    && docker-php-ext-enable redis; \
    fi

WORKDIR /var/www

# ======================================================================================================================
#  PHP-FPM STAGE
# ======================================================================================================================
FROM base AS php-fpm

EXPOSE 9000
CMD ["php-fpm"]

# ======================================================================================================================
#  SUPERVISOR STAGE
# ======================================================================================================================
FROM base AS supervisor

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update && apk add --no-cache supervisor \
    && mkdir -p /var/log/supervisor \
    && mkdir -p /var/run/supervisor

CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]

# ======================================================================================================================
#  WORKSPACE STAGE
# ======================================================================================================================
FROM base AS workspace

ARG NODE_VERSION

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Install Node.js
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update && apk add --no-cache nodejs npm yarn

EXPOSE 5173