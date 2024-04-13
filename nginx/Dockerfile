FROM ubuntu:20.04

LABEL image.version="0.0.1-beta" \
    image.release-date="2024-04-13" \
    image.name="funpa-nginx1.24.0" \
    image.authors="mengyilingjian@outlook.com"
# 设置时区
ENV TZ=Asia/Shanghai

ENV INSTALL_DIR=/www/server/nginx

MAINTAINER Eric

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y iproute2  ntpdate  tcpdump telnet traceroute nfs-kernel-server nfs-common  lrzsz tree  openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev ntpdate tcpdump telnet traceroute  gcc openssh-server lrzsz tree  openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev ntpdate tcpdump telnet traceroute iotop unzip zip make libxml2 libxslt1.1 libxml2-dev libxslt1-dev libgd-dev perl libperl-dev google-perftools libgoogle-perftools-dev tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    && wget -q https://nginx.org/download/nginx-1.24.0.tar.gz \
    && tar -xf nginx-1.24.0.tar.gz \
    && cd nginx-1.24.0 \
    && ./configure \
        --prefix=${INSTALL_DIR} \
        --with-compat --with-debug --with-file-aio --with-google_perftools_module --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_degradation_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_mp4_module --with-http_perl_module=dynamic --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-http_xslt_module=dynamic --with-mail=dynamic --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream=dynamic --with-stream_ssl_module --with-stream_ssl_preread_module --with-threads --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie' \
    && make && make install \
    && ln -sv ${INSTALL_DIR}/sbin/nginx /usr/bin \
    && rm -rf /nginx-1.24.* \
    && apt-get clean && rm -rf ${INSTALL_DIR}/nginx-1.24.0.tar.gz && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/*

#RUN groupadd  -g 2019 nginx && useradd  -g nginx -s /usr/sbin/nologin -u 2019  nginx && chown -R nginx.nginx /apps/nginx /data/nginx

#EXPOSE 80 443
ENTRYPOINT ["nginx"]
CMD ["-g","daemon off;"]
