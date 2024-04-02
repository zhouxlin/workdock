FROM debian:stable

## install php 
USER root
ARG PHP_VERSION
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y apt-transport-https lsb-release ca-certificates wget && \
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list && \
    apt update && \
    apt install -y php${PHP_VERSION} && \
    apt install -y php${PHP_VERSION}-mysql php${PHP_VERSION}-cli php${PHP_VERSION}-common php${PHP_VERSION}-imap php${PHP_VERSION}-ldap php${PHP_VERSION}-xml php${PHP_VERSION}-curl php${PHP_VERSION}-mbstring php${PHP_VERSION}-zip php${PHP_VERSION}-bcmath php${PHP_VERSION}-bz2 php${PHP_VERSION}-gd php${PHP_VERSION}-intl

# install composer
RUN wget -O composer-setup.php https://getcomposer.org/installer && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
    chmod +x /usr/local/bin/composer
# install certbot

# install supervisor

## install node
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt-get install -y nodejs && \
    apt-get install -y npm && \
    npm install -g yarn

WORKDIR /var/www/