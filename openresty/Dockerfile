FROM ubuntu:20.04

LABEL image.version="0.0.1-beta" \
    image.release-date="2024-04-13" \
    image.name="funpa-openresty" \
    image.authors="mengyilingjian@outlook.com"

# 设置时区
ENV TZ=Asia/Shanghai

MAINTAINER Eric

RUN set -eux \
    && apt-get update \
    && apt-get install -y \
        gcc \
        g++ \
        make \
        automake \
        autoconf \
        libtool \
        libpcre3-dev \
        zlib1g-dev \
        busybox-static \
        curl \
        libssl-dev \
        vim \
        lsb-release \
        wget \
        gnupg \
        ca-certificates \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    # 导入密钥
    && wget -qO - https://openresty.org/package/pubkey.gpg | apt-key add - \
    && echo "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main" > openresty.list \
    && cp openresty.list /etc/apt/sources.list.d/ \
    && apt-get update \
    && apt-get -y install openresty \
    && apt-get clean && rm -rf /usr/local/nginx/nginx-1.24.0.tar.gz && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/*

#暴露80端口
EXPOSE 80/tcp
ENTRYPOINT ["openresty"]
CMD ["-g","daemon off;"]
