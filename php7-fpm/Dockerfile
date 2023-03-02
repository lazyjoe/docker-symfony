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
    docker-php-ext-enable xdebug && \
    echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.discover_client_host=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.idekey=\"VSCODE\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "xdebug.client_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo "alias ll=\"ls -al --color=auto\"" >> ~/.bashrc && \
    echo "alias sf=\"php bin/console\"" >> ~/.bashrc && \
    curl -sSLo /usr/local/bin/phpunit https://phar.phpunit.de/phpunit-9.phar && \
    chmod +x /usr/local/bin/phpunit && \
    phpunit --version && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/symfony

# 第一次記得進去容器跑 composer install & yarn install & yarn build