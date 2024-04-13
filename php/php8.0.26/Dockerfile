# Stage 1: Build PHP container
FROM ubuntu:20.04

LABEL image.version="0.0.1-beta" \
    image.release-date="2024-04-13" \
    image.name="fun-php8.0.26" \
    image.authors="mengyilingjian@outlook.com"

MAINTAINER Eric

# 指定字符集
ENV LANG C.UTF-8
# 设置时区
ENV TZ=Asia/Shanghai
# 指定php安装目录
ENV PHP_INSTALL_DIR=/www/server/php80
# 指定软件包下载目录
ENV DOWNLOAD_DIR=/www/download

# 指定库和头文件的路径
ENV LD_LIBRARY_PATH="/usr/local/lib:${LD_LIBRARY_PATH}"
ENV C_INCLUDE_PATH="/usr/local/include:${C_INCLUDE_PATH}"

# 设置环境变量，告知debconf使用非交互式的前端
ENV DEBIAN_FRONTEND=noninteractive

#ENV PHP_USER www-data
#ENV PHP_GROUP www-data

# 设置环境变量，以便通过命令行使用 PHP
ENV PATH="${PHP_INSTALL_DIR}/bin:$PATH"

# 这个镜像需要添加 nobody 用户组
RUN groupadd -r nobody

# 安装依赖并编译 PHP
RUN set -eux \
    && apt-get update \
    && apt-get install -y aptitude \
    && DEBIAN_FRONTEND=noninteractive aptitude install -y -f vim wget curl gnupg2 php-pear php-dev apt-transport-https ca-certificates unzip unixodbc-dev g++ gcc make autoconf automake build-essential libc-dev pkg-config zlib1g-dev libxslt-dev sqlite3 libsqlite3-dev libc-ares-dev libcurl4-openssl-dev libzip-dev libpng-dev librdkafka-dev libargon2-0-dev libonig-dev libsodium-dev libxml2 libxml2-dev libssl-dev libjpeg-dev libpng-dev libfreetype6-dev libmcrypt-dev libreadline-dev libicu-dev net-tools tzdata\
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    # 下载php源码 \
    && mkdir -p ${DOWNLOAD_DIR} \
    && cd ${DOWNLOAD_DIR} \
    && wget -q https://secure.php.net/distributions/php-8.0.26.tar.gz \
    && tar -xf php-8.0.26.tar.gz \
    && cd php-8.0.26 \
    && ./configure \
        --prefix=${PHP_INSTALL_DIR} \
        --with-config-file-path=${PHP_INSTALL_DIR}/etc \
        --enable-fpm \
        --enable-bcmath \
        --enable-calendar \
        --with-curl \
        --enable-exif \
        --enable-ftp \
        --with-gd \
        --with-gettext \
        --enable-mbstring \
        --with-openssl \
        --enable-pcntl \
        --with-mysqli \
        --enable-mysqlnd \
        --enable-pdo \
        --with-pdo_mysql \
        --enable-sockets \
        --with-sodium \
        --enable-sysvmsg \
        --enable-sysvsem \
        --enable-sysvshm \
        --enable-wddx \
        --with-xsl \
        --enable-zip \
        --with-zlib \
        --enable-opcache \
        --enable-shmop \
        --with-readline \
    && make -j "$(nproc)" \
    && make install

# php 配置文件
RUN cp ${PHP_INSTALL_DIR}/etc/php-fpm.conf.default ${PHP_INSTALL_DIR}/etc/php-fpm.conf \
    # wsl要关闭这个选项
    && sed -i "21a \daemonize = no" ${PHP_INSTALL_DIR}/etc/php-fpm.conf \
    && cp ${PHP_INSTALL_DIR}/etc/php-fpm.d/www.conf.default ${PHP_INSTALL_DIR}/etc/php-fpm.d/www.conf \
#    修改php执行用户
#    && sed -i "s/user = nobody/user = ${PHP_USER}/" ${PHP_INSTALL_DIR}/etc/php-fpm.d/www.conf \
#    && sed -i "s/group = nobody/group = ${PHP_GROUP}/" ${PHP_INSTALL_DIR}/etc/php-fpm.d/www.conf \
#    端口指定
    && sed -i "s|^listen = 127.0.0.1:9000$|listen = 0.0.0.0:9001|" ${PHP_INSTALL_DIR}/etc/php-fpm.d/www.conf \
    && cp ${DOWNLOAD_DIR}/php-8.0.26/php.ini-development ${PHP_INSTALL_DIR}/etc/php.ini \
    && echo "extension_dir=\"${PHP_INSTALL_DIR}/lib/php/extensions/no-debug-non-zts-20200930\"" >> ${PHP_INSTALL_DIR}/etc/php.ini

# 编译非内置扩展
# 安装igbinary
RUN cd ${DOWNLOAD_DIR} \
    && wget -q https://pecl.php.net/get/igbinary-3.2.15.tgz && tar -zxvf igbinary-3.2.15.tgz \
    && mv igbinary-3.2.15 igbinary-src \
    && cd igbinary-src \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --prefix=${PHP_INSTALL_DIR} --with-php-config=${PHP_INSTALL_DIR}/bin/php-config \
    && make -j "$(nproc)" \
    && make install \
    && sed -i "2i extension=igbinary.so" ${PHP_INSTALL_DIR}/etc/php.ini \
    # 编译和安装redis
    && cd ${DOWNLOAD_DIR}/ \
        && wget -q https://pecl.php.net/get/redis-5.3.4.tgz && tar -zxvf redis-5.3.4.tgz \
        && mv redis-5.3.4 redis-src \
        && cd redis-src \
        && ${PHP_INSTALL_DIR}/bin/phpize \
        && ./configure --prefix=${PHP_INSTALL_DIR} --with-php-config=${PHP_INSTALL_DIR}/bin/php-config \
        && make -j "$(nproc)" \
        && make install \
        && sed -i "2i extension=redis.so" ${PHP_INSTALL_DIR}/etc/php.ini \
    # 编译yac扩展
    && cd ${DOWNLOAD_DIR}/ \
        && wget -q https://pecl.php.net/get/yac-2.3.1.tgz && tar -zxvf yac-2.3.1.tgz \
        && mv yac-2.3.1 yac-src \
        && cd yac-src \
        && ${PHP_INSTALL_DIR}/bin/phpize \
        && ./configure --prefix=${PHP_INSTALL_DIR} --with-php-config=${PHP_INSTALL_DIR}/bin/php-config \
        && make -j "$(nproc)" \
        && make install \
        && sed -i "2i extension=yac.so" ${PHP_INSTALL_DIR}/etc/php.ini \
    # rdkafka
    && cd ${DOWNLOAD_DIR}/ \
        && wget -q https://pecl.php.net/get/rdkafka-6.0.3.tgz && tar -zxvf rdkafka-6.0.3.tgz \
        && mv rdkafka-6.0.3 rdkafka-src \
        && cd rdkafka-src \
        && ${PHP_INSTALL_DIR}/bin/phpize \
        && ./configure --prefix=${PHP_INSTALL_DIR} --with-php-config=${PHP_INSTALL_DIR}/bin/php-config \
        && make -j "$(nproc)" \
        && make install \
        && sed -i "2i extension=rdkafka.so" ${PHP_INSTALL_DIR}/etc/php.ini \
    # swoole
    && cd ${DOWNLOAD_DIR}/ \
        && wget -q https://pecl.php.net/get/swoole-5.1.1.tgz && tar -zxvf swoole-5.1.1.tgz \
        && mv swoole-5.1.1 swoole-src \
        && cd swoole-src \
        && ${PHP_INSTALL_DIR}/bin/phpize \
        && ./configure --prefix=${PHP_INSTALL_DIR} --with-php-config=${PHP_INSTALL_DIR}/bin/php-config --enable-swoole-curl --enable-cares --enable-http2 --enable-swoole-json \
        && make -j "$(nproc)" \
        && make install \
        && sed -i "2i extension=swoole.so" ${PHP_INSTALL_DIR}/etc/php.ini

# 清理不需要的软件包和缓存
RUN apt-get clean && rm -rf ${DOWNLOAD_DIR} && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/*

# 配置 PHP 时区
RUN date_default_timezone_set='Asia/Shanghai'

WORKDIR ${PHP_INSTALL_DIR}

# 暴露 php 端口
EXPOSE 9001

## 启动 PHP-FPM 服务
CMD ["/www/server/php80/sbin/php-fpm"]
