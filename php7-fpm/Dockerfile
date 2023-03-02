# 构建基础镜像
FROM php:7.4-fpm
ARG TIMEZONE

RUN echo "memory_limit=512M" > /usr/local/etc/php/conf.d/memory-limit.ini && apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libpng-dev && \
    apt-get install -y python2 && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-install pdo pdo_mysql && \
    docker-php-ext-install gd && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer --version && \
    curl -sL https://deb.nodesource.com/setup_14.x | bash - && \
    apt-get install -y nodejs && \
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y yarn && \
    ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone && \
    printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini && \
    pecl install xdebug-3.0.4 && \
    docker-php-ext-enable xdebug # && \
    curl -sSLo /usr/local/bin/phpunit https://phar.phpunit.de/phpunit-9.phar && \
    chmod +x /usr/local/bin/phpunit && \
    phpunit --version && \
    rm -rf /var/lib/apt/lists/*

ENV XDEBUG_MODE=debug
ENV XDEBUG_CONFIG="remote_enable=1 remote_mode=req client_port=9001 client_host=host.docker.internal discover_client_host=0"
# idekey=\"VSCODE\" 可以不用加

WORKDIR /var/www/symfony

# 第一次記得進去容器跑 composer install & yarn install & yarn build｀