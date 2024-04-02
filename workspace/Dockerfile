FROM debian:stable

## install php 
USER root
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y apt-transport-https lsb-release ca-certificates wget && \
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list && \
    apt update && \
    apt install -y php8.3 && \
    apt install -y php8.3-mysql php8.3-cli php8.3-common php8.3-imap php8.3-ldap php8.3-xml php8.3-curl php8.3-mbstring php8.3-zip php8.3-bcmath php8.3-bz2 php8.3-gd php8.3-intl

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