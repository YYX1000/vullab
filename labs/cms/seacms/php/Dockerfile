FROM php:7.4-apache

RUN apt-get update && apt-get install -y --no-install-recommends \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libcurl4-openssl-dev \
        curl \
        unzip \
    && rm -rf /var/lib/apt/lists/*

# 编译安装Seacms必备扩展 gd curl mysqli
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd curl mysqli

# 额外安装iconv，seacms编码依赖
RUN docker-php-ext-install iconv

# php配置
RUN echo "allow_url_fopen = On" >> /usr/local/etc/php/conf.d/seacms.ini \
    && echo "date.timezone = Asia/Shanghai" >> /usr/local/etc/php/conf.d/seacms.ini

# 开启apache伪静态rewrite模块
RUN a2enmod rewrite

# 允许.htaccess覆盖配置
RUN sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf

WORKDIR /var/www/html

# 下载zip，解压，只把 upload/ 里面全部文件拷贝到网站根目录
RUN curl -L https://www.seacms.net/download/%E5%AE%89%E8%A3%85%E5%8C%85/SeaCMS_V13.3_install.zip -o /tmp/SeaCMS_V13.3_install.zip \
    && unzip -q /tmp/SeaCMS_V13.3_install.zip -d /tmp/seacms_src \
    && rm -f /tmp/SeaCMS_V13.3_install.zip \
    && cp -r /tmp/seacms_src/SeaCMS/Upload/* /var/www/html/ \
    && rm -rf /tmp/seacms_src \
    && chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html