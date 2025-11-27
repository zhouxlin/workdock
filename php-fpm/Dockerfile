ARG PHP_VERSION
# 切换到 Alpine 版本
FROM php:${PHP_VERSION}-fpm-alpine

# 1. 使用 apk 替换 apt-get，并安装 PHP 扩展所需的依赖
# 将所有操作放在一个 RUN 命令中，确保在清理构建依赖后，最终镜像尽可能小。
#RUN sed -i 's|https://|http://|g' /etc/apk/repositories
RUN set -eux; \
    # 临时安装构建依赖 (build-deps)
    apk update; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        mariadb-connector-c-dev \
        libzip-dev \
    ; \
    \
    # 安装运行时所需的工具包
    apk add --no-cache \
        zip \
        sqlite \
    ; \
    \
    # 安装 PHP 扩展 (依赖于上面的 -dev 包)
    # && docker-php-ext-configure zip --with-libzip \
    docker-php-ext-install pdo_mysql zip; \
    \
    # 清理构建依赖和缓存，这是 Alpine 镜像保持精简的关键
    apk del .build-deps; \
    rm -rf /var/cache/apk/*

# install composer
# COPY --from=composer/composer:latest-bin /composer /usr/local/bin/composer

CMD ["php-fpm"]
WORKDIR /var/www