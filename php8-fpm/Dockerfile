# 构建基础镜像
FROM php:8.4-fpm
ARG TIMEZONE

ENV ALIAS1="alias ll='lsd -al --color=auto'"
ENV ALIAS2="alias hg='history|grep -v grep|grep -v hg|grep'"
ENV ALIAS3="alias pag='ps aux|grep -v grep|grep -v pag|grep'"
ENV ALIAS4="alias phpunit='vendor/bin/phpunit --colors=always'"
ENV ALIAS5="alias pf='vendor/bin/phpunit --colors=always --filter'"
ENV ALIAS6="alias sf='php bin/console'"
ENV ALIAS7="alias clr='php bin/console cache:clear --env=test'"
ENV ALIAS8="alias phpfmt='vendor/bin/phpcbf --standard=PSR12'"
ENV ALIAS9="alias phpstan='vendor/bin/phpstan analyse --memory-limit=2G -c phpstan.neon'"

# Configure PHP to suppress deprecation warnings and prevent them from being output to browser
RUN echo "memory_limit=512M" > /usr/local/etc/php/conf.d/memory-limit.ini && \
    echo "error_reporting = E_ALL & ~E_DEPRECATED & ~E_USER_DEPRECATED" > /usr/local/etc/php/conf.d/error-reporting.ini && \
    echo "display_errors = Off" > /usr/local/etc/php/conf.d/display-errors.ini && \
    echo "log_errors = On" > /usr/local/etc/php/conf.d/log-errors.ini

# Install system dependencies
RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libwebp-dev \
    libxpm-dev \
    python3 \
    autossh \
    iputils-ping \
    procps \
    libzip-dev \
    libicu-dev \
    librabbitmq-dev \
    gnupg \
    build-essential \
    zlib1g-dev \
    libncurses5-dev \
    libgdbm-dev \
    libnss3-dev \
    libssl-dev \
    libreadline-dev \
    libffi-dev \
    libsqlite3-dev \
    wget \
    libbz2-dev \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions with complete GD support
RUN docker-php-ext-configure gd \
    --with-freetype \
    --with-jpeg \
    --with-webp \
    --with-xpm && \
    docker-php-ext-install pdo pdo_mysql zip gd intl

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Python 2.7.18 from source (optimized build)
RUN cd /tmp && \
    wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz && \
    tar -xzf Python-2.7.18.tgz && \
    cd Python-2.7.18 && \
    ./configure --prefix=/usr/local/python2.7 --enable-shared && \
    make -j$(nproc) && \
    make altinstall && \
    ln -sf /usr/local/python2.7/bin/python2.7 /usr/local/bin/python2 && \
    ln -sf /usr/local/python2.7/bin/python2.7 /usr/local/bin/python2.7 && \
    echo "/usr/local/python2.7/lib" > /etc/ld.so.conf.d/python2.7.conf && \
    ldconfig && \
    cd / && \
    rm -rf /tmp/Python-2.7.18*

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone && \
    printf '[PHP]\ndate.timezone = "%s"\n' ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini

# Install PECL extensions
RUN pecl install xdebug-3.4.0 apcu amqp && \
    docker-php-ext-enable xdebug apcu amqp

# Set up aliases
RUN echo "$ALIAS1" >> ~/.bashrc && \
    echo "$ALIAS2" >> ~/.bashrc && \
    echo "$ALIAS3" >> ~/.bashrc && \
    echo "$ALIAS4" >> ~/.bashrc && \
    echo "$ALIAS5" >> ~/.bashrc && \
    echo "$ALIAS6" >> ~/.bashrc && \
    echo "$ALIAS7" >> ~/.bashrc && \
    echo "$ALIAS8" >> ~/.bashrc && \
    echo "$ALIAS9" >> ~/.bashrc && \
    echo "source ~/.bashrc" >> ~/.bash_profile

# Install Yarn and lsd
RUN curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o /usr/share/keyrings/yarn-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/yarn-keyring.gpg] https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install -y yarn lsd && \
    rm -rf /var/lib/apt/lists/*

# Install NVM and Node.js 12
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    nvm install 12 && \
    nvm use 12 && \
    nvm alias default 12

WORKDIR /var/www/symfony

ENV XDEBUG_MODE=debug
# mac uses host.docker.internal & linux use php then add "172.sth.0.1 php" to /etc/hosts
#ENV XDEBUG_CONFIG="remote_enable=1 remote_mode=req client_port=9001 discover_client_host=0 idekey=VSCODE client_host=172.18.0.1"
ENV XDEBUG_CONFIG="remote_enable=1 remote_mode=req client_port=9001 discover_client_host=0 idekey=VSCODE client_host=host.docker.internal"
# 第一次記得進去容器跑 composer install & nvm install 12 & yarn install & (yarn dev or yarn build)｀