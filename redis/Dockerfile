FROM ubuntu:20.04

WORKDIR /data

ENV PASSWORD=ZHKPctsnugxgRcPm8Ezwbt3n
ENV REDIS_PORT=6379

ENV INSTALL_DIR=/usr/local/redis
# 指定字符集
ENV LANG C.UTF-8
# 设置时区
ENV TZ=Asia/Shanghai

RUN set -eux \
    && mkdir -p /www/wwwlogs/redis \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata \
        vim \
        wget \
        rsync \
        gcc \
        make \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    # 下载pika源码 \
    && wget -q https://download.redis.io/releases/redis-7.2.4.tar.gz \
    && tar -xf redis-7.2.4.tar.gz \
    && cd redis-7.2.4 \
    && make && make install PREFIX=${INSTALL_DIR} && cp redis.conf ${INSTALL_DIR} \
    && cd ${INSTALL_DIR}/bin && cp * /usr/local/bin/ \
    && sed -i "s/bind 127.0.0.1 -::1$/# bind 127.0.0.1 -::1/" ${INSTALL_DIR}/redis.conf \
    && sed -i "s/port 6379$/port ${REDIS_PORT}/" ${INSTALL_DIR}/redis.conf \
    && sed -i "s/protected-mode yes$/protected-mode no/" ${INSTALL_DIR}/redis.conf \
    && sed -i "s/daemonize no$/daemonize yes/" ${INSTALL_DIR}/redis.conf \
    && sed -i 's/logfile ""/logfile "\/www\/wwwlogs\/redis\/redis.log"/g' ${INSTALL_DIR}/redis.conf \
    && sed -i "s/# requirepass foobared$/requirepass ${PASSWORD}/" ${INSTALL_DIR}/redis.conf

RUN apt-get clean && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/* && rm -rf /data/redis-*
#${INSTALL_DIR}/bin/redis-server ${INSTALL_DIR}/redis.conf --supervised systemd
EXPOSE ${REDIS_PORT}
CMD ["redis-server", "/usr/local/redis/redis.conf"]
