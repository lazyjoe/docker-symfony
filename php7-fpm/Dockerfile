# 构建基础镜像
FROM php:8.2-fpm
ARG TIMEZONE

ENV ALIAS1="alias ll='ls -al --color=auto'"
ENV ALIAS2="alias hg='history|grep -v grep|grep -v hg|grep'"
ENV ALIAS3="alias pag='ps aux|grep -v grep|grep -v pag|grep'"
ENV ALIAS4="alias phpunit='vendor/bin/phpunit --colors=always'"
ENV ALIAS5="alias pf='vendor/bin/phpunit --colors=always --filter'"
ENV ALIAS6="alias sf='php bin/console'"
ENV ALIAS7="alias clr='php bin/console cache:clear --env=test'"

RUN echo "memory_limit=512M" > /usr/local/etc/php/conf.d/memory-limit.ini && \
    curl -sL https://deb.nodesource.com/setup_14.x | bash - && \
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libpng-dev \
    libjpeg62-turbo-dev \
    python3 \
    nodejs \
    autossh \
    iputils-ping \
    procps \
    libzip-dev \
    yarn && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --with-jpeg=/usr/include/ && \
    docker-php-ext-install pdo pdo_mysql zip gd && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer --version && \
    ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone && \
    printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini && \
    pecl install xdebug apcu && \
    docker-php-ext-enable xdebug apcu && \
    echo "$ALIAS1" >> ~/.bashrc && \
    echo "$ALIAS2" >> ~/.bashrc && \
    echo "$ALIAS3" >> ~/.bashrc && \
    echo "$ALIAS4" >> ~/.bashrc && \
    echo "$ALIAS5" >> ~/.bashrc && \
    echo "$ALIAS6" >> ~/.bashrc && \
    echo "$ALIAS7" >> ~/.bashrc && \
    echo "source ~/.bashrc" >> ~/.bash_profile && \
    rm -rf /var/lib/apt/lists/*

ENV XDEBUG_MODE=debug
ENV XDEBUG_CONFIG="remote_enable=1 remote_mode=req client_port=9001 client_host=host.docker.internal discover_client_host=0 idekey=VSCODE"

WORKDIR /var/www/symfony

# 第一次記得進去容器跑 composer install & yarn install & yarn build｀
