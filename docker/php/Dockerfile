FROM php:8.2-fpm AS base

ARG DEBIAN_FRONTEND=noninteractive
ARG GID=1000
ARG UID=1000

RUN apt update && \
    # Install tools
    apt install -y --no-install-recommends \
        libxml2-utils \
        wget \
        git \
        unzip && \
    # Install intl
    apt install -y --no-install-recommends libicu-dev && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl && \
    # Install bcmath
    docker-php-ext-install bcmath && \
    # Install zip
    apt install -y --no-install-recommends libzip-dev && \
    docker-php-ext-install zip && \
    # Install sockets
    docker-php-ext-install sockets && \
    # Install apcu
    pecl install apcu && docker-php-ext-enable apcu && \
    # Install xdebug
    pecl install xdebug-3.2.0

# Install gosu
RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.14/gosu-$(dpkg --print-architecture)" && \
    chmod +x /usr/local/bin/gosu

# Install composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN groupadd --gid ${GID} user && useradd --uid ${UID} -s /bin/bash -m -g user user

WORKDIR /data

RUN chown -R ${GID}:${UID} /data

########################################
FROM base as prod

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

RUN sed -ri "s#;opcache.preload=#opcache.preload=/data/config/preload.php#" "$PHP_INI_DIR/php.ini"
RUN sed -ri "s#;opcache.enable_cli=#opcache.enable_cli=1#" "$PHP_INI_DIR/php.ini"

########################################
FROM base as dev

RUN docker-php-ext-enable xdebug
RUN chmod 777 /opt

RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

RUN echo "apc.enable_cli=1" >> "$PHP_INI_DIR/conf.d/docker-php-ext-apcu.ini"
RUN sed -ri "s#memory_limit.*#memory_limit = 1G#" "$PHP_INI_DIR/php.ini"
