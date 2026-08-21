FROM php:8.1-fpm

RUN apt-get update && apt-get install -y --no-install-recommends \
    unzip \
    curl \
    libzip-dev \
    libpng-dev \
    libjpeg-dev \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install zip gd mysqli pdo_mysql

WORKDIR /var/www/html

# 容器内部直接下载wordpress 7.0.1官方tag压缩包，不再依赖本地zip
RUN curl -L https://github.com/WordPress/WordPress/archive/refs/tags/7.0.1.zip -o /tmp/7.0.1.zip \
    && unzip -q /tmp/7.0.1.zip -d /tmp/src \
    && rm /tmp/7.0.1.zip \
    && cp -r /tmp/src/WordPress-7.0.1/* /var/www/html/ \
    && rm -rf /tmp/src \
    && chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html

EXPOSE 9000