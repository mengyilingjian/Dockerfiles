FROM ubuntu:20.04

WORKDIR /data

ENV PASSWORD=ZHKPctsnugxgRcPm8Ezwbt3n
ENV LOG_PATH=/www/wwwlogs/pika

ENV INSTALL_DIR=/usr/local
# 指定字符集
ENV LANG C.UTF-8
# 设置时区
ENV TZ=Asia/Shanghai

RUN set -eux \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata \
        vim \
        wget \
        rsync \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    # 下载pika源码 \
    && cd /data && wget -q https://github.com/OpenAtomFoundation/pika/releases/download/v3.3.6/pika-linux-x86_64-v3.3.6.tar.bz2 \
    && tar -C ${INSTALL_DIR} -jxvf ./pika-linux-x86_64-v3.3.6.tar.bz2 \
    && mv ${INSTALL_DIR}/output/ ${INSTALL_DIR}/pika \
    && sed -i "s/^requirepass :$/requirepass : ${PASSWORD}/" ${INSTALL_DIR}/pika/conf/pika.conf \
    && sed -i "s|^log-path : ./log/$|log-path : ${LOG_PATH}|" ${INSTALL_DIR}/pika/conf/pika.conf

RUN apt-get clean && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/* && rm -rf /data/pika-linux-*

EXPOSE 9221

#ENTRYPOINT ["/pika/entrypoint.sh"]
CMD ["/usr/local/pika/bin/pika", "-c", "/usr/local/pika/conf/pika.conf"]
