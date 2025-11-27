ARG PHP_VERSION
# 切换到 Alpine 版本
FROM php:${PHP_VERSION}-fpm-alpine

# 1. 使用 apk 替换 apt-get，并安装 PHP 扩展所需的依赖
# 将所有操作放在一个 RUN 命令中，确保在清理构建依赖后，最终镜像尽可能小。
RUN set -eux; \
    \
    # =========================================================================
    # 新的解决方案：强制写入官方 HTTP 镜像源，解决可能的 SSL/TLS 握手超时问题。
    # -------------------------------------------------------------------------
    # 使用 HTTP 协议访问官方 CDN
    echo "http://dl-cdn.alpinelinux.org/alpine/v3.22/main" > /etc/apk/repositories; \
    echo "http://dl-cdn.alpinelinux.org/alpine/v3.22/community" >> /etc/apk/repositories; \
    # =========================================================================
    \
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
COPY --from=composer/composer:latest-bin /composer /usr/local/bin/composer

CMD ["php-fpm"]
WORKDIR /var/www