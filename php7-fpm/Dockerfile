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
    curl -sL https://deb.nodesource.com/setup_17.x | bash - && \
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libpng-dev \
    libjpeg62-turbo-dev \
    python3 \
    autossh \
    iputils-ping \
    procps \
    libzip-dev \
    libicu-dev \
    lsd \
    yarn \
    librabbitmq-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --with-jpeg=/usr/include/ && \
    docker-php-ext-install pdo pdo_mysql zip gd intl && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer --version && \
    ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone && \
    printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini && \
    pecl install xdebug apcu amqp && \
    docker-php-ext-enable xdebug apcu amqp && \
    echo "$ALIAS1" >> ~/.bashrc && \
    echo "$ALIAS2" >> ~/.bashrc && \
    echo "$ALIAS3" >> ~/.bashrc && \
    echo "$ALIAS4" >> ~/.bashrc && \
    echo "$ALIAS5" >> ~/.bashrc && \
    echo "$ALIAS6" >> ~/.bashrc && \
    echo "$ALIAS7" >> ~/.bashrc && \
    echo "source ~/.bashrc" >> ~/.bash_profile && \
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash && \
    rm -rf /var/lib/apt/lists/*

ENV XDEBUG_MODE=debug
ENV XDEBUG_CONFIG="remote_enable=1 remote_mode=req client_port=9001 client_host=host.docker.internal discover_client_host=0 idekey=VSCODE"

WORKDIR /var/www/symfony
ENV ALIAS8="alias phpfmt='vendor/bin/phpcbf --standard=PSR12'"
ENV ALIAS9="alias phpstan='vendor/bin/phpstan analyse --memory-limit=2G -c phpstan.neon'"
ENV ALIAS10="alias ll='lsd -al'"
RUN echo "$ALIAS8" >> ~/.bashrc && \
    echo "$ALIAS9" >> ~/.bashrc && \
    echo "$ALIAS10" >> ~/.bashrc && \
    echo "source ~/.bashrc" >> ~/.bash_profile

# 第一次記得進去容器跑 composer install & nvm install 12 & yarn install & yarn build｀
