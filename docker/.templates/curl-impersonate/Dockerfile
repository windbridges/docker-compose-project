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

###########################################################
# CURL Impersonate
###########################################################
# Brotli
RUN git clone --recursive --depth=1 https://github.com/kjdev/php-ext-brotli.git /usr/local/php-ext-brotli && \
        cd /usr/local/php-ext-brotli && \
        phpize && \
        ./configure && \
        make && \
        make install && \
        docker-php-ext-enable brotli

# Install curl-impersonate
RUN apt-get install -y build-essential pkg-config cmake ninja-build curl autoconf automake libtool zlib1g-dev && \
    apt-get install -y golang-go unzip && \
    git clone https://github.com/lwthiker/curl-impersonate.git && \
    cd curl-impersonate && \
    mkdir build && cd build && \
    ../configure && \
#    USE_CURL_SSLKEYLOGFILE=true ../configure && \
    make chrome-build && \
    make chrome-install && \
    ldconfig && \
    cd ../ && rm -Rf build

RUN apt-get install -y patchelf \
    && patchelf --set-soname libcurl.so.4 /usr/local/lib/libcurl-impersonate-chrome.so \
    && echo "/usr/local/lib/libcurl-impersonate-chrome.so" > /etc/ld.so.preload

ENV CURL_IMPERSONATE=chrome110
ENV CURL_IMPERSONATE_HEADERS=no
###########################################################

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
