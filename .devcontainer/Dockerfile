FROM mcr.microsoft.com/devcontainers/base:ubuntu24.04

LABEL maintainer="Patrick Henninger"

ARG WWWGROUP=1000
ARG NODE_VERSION=24
ARG PHP_VERSION
ARG PHP_EXTENSIONS=""

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC
ENV LANG=C.UTF-8

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN echo "Acquire::http::Pipeline-Depth 0;" > /etc/apt/apt.conf.d/99custom && \
    echo "Acquire::http::No-Cache true;" >> /etc/apt/apt.conf.d/99custom && \
    echo "Acquire::BrokenProxy    true;" >> /etc/apt/apt.conf.d/99custom

RUN apt-get update && apt-get upgrade -y \
    && mkdir -p /etc/apt/keyrings \
    && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python3 dnsutils librsvg2-bin fswatch ffmpeg nano \
    && curl -sSLo /tmp/debsuryorg-archive-keyring.deb https://packages.sury.org/debsuryorg-archive-keyring.deb \
    && dpkg -i /tmp/debsuryorg-archive-keyring.deb \
    && echo "deb [signed-by=/usr/share/keyrings/debsuryorg-archive-keyring.gpg] https://packages.sury.org/php/ noble main" > /etc/apt/sources.list.d/php.list \
    && apt-get update \
    && apt-get install -y \
    libgd3 \
    php$PHP_VERSION-cli \
    php$PHP_VERSION-dev \
    php$PHP_VERSION-pgsql \
    php$PHP_VERSION-sqlite3 \
    php$PHP_VERSION-gd \
    php$PHP_VERSION-curl \
    php$PHP_VERSION-mongodb \
    php$PHP_VERSION-imap \
    php$PHP_VERSION-mysql \
    php$PHP_VERSION-mbstring \
    php$PHP_VERSION-xml \
    php$PHP_VERSION-zip \
    php$PHP_VERSION-bcmath \
    php$PHP_VERSION-soap \
    php$PHP_VERSION-intl \
    php$PHP_VERSION-readline \
    php$PHP_VERSION-ldap \
    php$PHP_VERSION-msgpack \
    php$PHP_VERSION-igbinary \
    php$PHP_VERSION-redis \
    php$PHP_VERSION-swoole \
    php$PHP_VERSION-memcached \
    php$PHP_VERSION-pcov \
    php$PHP_VERSION-imagick \
    php$PHP_VERSION-xdebug \
    && curl -sLS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer \
    && COMPOSER_HOME=/usr/local/share/composer composer global require cpx/cpx:^2.0 \
    && ln -s /usr/local/share/composer/vendor/bin/cpx /usr/local/bin/cpx \
    && curl -fsSL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm pnpm bun corepack \
    && corepack enable \
    && corepack prepare yarn@stable --activate \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN if [ -n "$PHP_EXTENSIONS" ]; then \
    apt-get update \
    && apt-get install -y $(for ext in $PHP_EXTENSIONS; do echo "php$PHP_VERSION-$ext"; done) \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*; \
    fi

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php$PHP_VERSION

COPY php.ini /etc/php/$PHP_VERSION/cli/conf.d/98-devcontainer.ini
COPY $PHP_VERSION/php.ini /etc/php/$PHP_VERSION/cli/conf.d/99-devcontainer-$PHP_VERSION.ini