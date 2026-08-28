# ============================================================
# SeaCMS 6.x 时代构建（PHP5.6 + apache）
#   PHP5.6（jessie 已归档，需切换 archive.debian.org 源）
#   构建时按 ARG 拉取 6.x 源码树（仓库根即站点根）
# ============================================================

FROM php:5.6-apache

# stretch 已 EOL 归档：覆盖为 archive.debian.org 单一源（trusted 跳过过期签名），移除失效子源
RUN echo "deb [trusted=yes] http://archive.debian.org/debian stretch main" > /etc/apt/sources.list \
    && apt-get -o Acquire::Check-Valid-Until=false update \
    && apt-get install -y --no-install-recommends --allow-unauthenticated \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libcurl4-openssl-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# 编译安装 gd curl mysqli mysql（6.x 代码兼容两种扩展）
RUN docker-php-ext-configure gd --with-freetype-dir=/usr --with-jpeg-dir=/usr --with-png-dir=/usr \
    && docker-php-ext-install -j$(nproc) gd curl mysqli mysql

# php 配置：6.x 模板依赖短标签与远程采集
RUN echo "short_open_tag = On" > /usr/local/etc/php/conf.d/seacms.ini \
    && echo "allow_url_fopen = On" >> /usr/local/etc/php/conf.d/seacms.ini \
    && echo "date.timezone = Asia/Shanghai" >> /usr/local/etc/php/conf.d/seacms.ini

# 开启 apache 伪静态 rewrite 模块，允许 .htaccess 覆盖
RUN a2enmod rewrite \
    && sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf

WORKDIR /var/www/html

# 源码仓库：由 docker-compose build.args 从 .env 注入（仓库根即站点根）
ARG SEACMS_REPO
RUN git clone --depth 1 "${SEACMS_REPO}" /tmp/seacms_src \
    && rm -rf /tmp/seacms_src/.git /tmp/seacms_src/.gitignore /tmp/seacms_src/.DS_Store /tmp/seacms_src/documents \
    && cp -r /tmp/seacms_src/. /var/www/html/ \
    && rm -rf /tmp/seacms_src \
    && chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html
