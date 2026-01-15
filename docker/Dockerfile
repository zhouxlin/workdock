ARG PHP_VERSION=8.2
ARG NODE_VERSION=20

# ======================================================================================================================
#  BASE STAGE
# ======================================================================================================================
FROM php:${PHP_VERSION}-fpm AS base

ARG INSTALL_BZ2=false
ARG INSTALL_GD=false
ARG INSTALL_LDAP=false
ARG INSTALL_PGSQL=false
ARG INSTALL_REDIS=false

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    gnupg \
    ca-certificates \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    libicu-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libldap2-dev \
    libbz2-dev \
    sqlite3 \
    libsqlite3-dev \
    default-mysql-client \
    vim \
    $( [ "$INSTALL_PGSQL" = "true" ] && echo "libpq-dev" ) \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

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

RUN apt-get update && apt-get install -y supervisor \
    && apt-get clean && rm -rf /var/lib/apt/lists/* \
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
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g yarn \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

EXPOSE 5173