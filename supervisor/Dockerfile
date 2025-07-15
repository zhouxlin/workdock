ARG PHP_VERSION
FROM php:${PHP_VERSION}-cli


RUN apt-get update && apt-get install -y \
    libzip-dev \
    zip \ 
    sqlite3 \
    # && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install pdo_mysql zip 

# 安装 Supervisor
# 注意：这里我们同时安装了 Supervisor，并且创建了日志目录和日志文件
RUN apt-get update && apt-get install -y \
    supervisor \
    && mkdir -p /var/log/supervisor \
    && touch /var/log/supervisor/supervisord.log \
    && mkdir -p /var/log/supervisor \
    && mkdir -p /var/run \
    && mkdir -p /etc/supervisor/conf.d

    
WORKDIR /var/www

CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]

