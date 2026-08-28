# ============================================================
# SeaCMS 10.x 时代构建（PHP7.4 + apache）
#   构建时按 ARG 拉取完整安装包 zip，解压取 */Upload/* 为站点根
# ============================================================

FROM php:7.4-apache

RUN apt-get update && apt-get install -y --no-install-recommends \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libcurl4-openssl-dev \
        curl \
        unzip \
    && rm -rf /var/lib/apt/lists/*

# 编译安装 seacms 必备扩展 gd curl mysqli
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd curl mysqli

# 额外安装 iconv，seacms 编码依赖
RUN docker-php-ext-install iconv

# php 配置
RUN echo "allow_url_fopen = On" >> /usr/local/etc/php/conf.d/seacms.ini \
    && echo "date.timezone = Asia/Shanghai" >> /usr/local/etc/php/conf.d/seacms.ini

# 开启 apache 伪静态 rewrite 模块
RUN a2enmod rewrite

# 允许 .htaccess 覆盖配置
RUN sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf

WORKDIR /var/www/html

# 完整安装包 zip：由 docker-compose build.args 从 .env 注入
# 安装包内部结构为 <顶层目录>/Upload/*（顶层目录名任意，含 GBK 名，通配符统一处理）
ARG SEACMS_ZIP_URL
RUN curl -fsSL "${SEACMS_ZIP_URL}" -o /tmp/seacms.zip \
    && unzip -q /tmp/seacms.zip -d /tmp/seacms_src \
    && rm -f /tmp/seacms.zip \
    && cp -r /tmp/seacms_src/*/Upload/* /var/www/html/ \
    && rm -rf /tmp/seacms_src \
    && chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html
