# PHP-Dockerfiles

### 介绍
php项目的中间件的dockerfile，并且使用docker-compose部署管理。都是基于源码编译，可指定配置文件和安装目录，自由定制化。

### 软件版本
- nginx1.24.0
- openresty:latest
- php7.3.32
  - [PHP Modules]
    bcmath
    calendar
    Core
    ctype
    curl
    date
    dom
    exif
    fileinfo
    filter
    ftp
    gd
    gettext
    hash
    iconv
    igbinary
    json
    libxml
    mbstring
    mysqli
    mysqlnd
    openssl
    pcntl
    pcre
    PDO
    pdo_mysql
    pdo_sqlite
    Phar
    posix
    rdkafka
    readline
    redis
    Reflection
    session
    shmop
    SimpleXML
    sockets
    sodium
    SPL
    sqlite3
    standard
    swoole
    sysvmsg
    sysvsem
    sysvshm
    tokenizer
    wddx
    xml
    xmlreader
    xmlwriter
    xsl
    yac
    zip
    zlib
- php8.0.26
  - [PHP Modules]
    bcmath
    calendar
    Core
    ctype
    curl
    date
    dom
    exif
    fileinfo
    filter
    ftp
    gettext
    hash
    iconv
    igbinary
    json
    libxml
    mbstring
    mysqli
    mysqlnd
    openssl
    pcntl
    pcre
    PDO
    pdo_mysql
    pdo_sqlite
    Phar
    posix
    rdkafka
    readline
    redis
    Reflection
    session
    shmop
    SimpleXML
    sockets
    sodium
    SPL
    sqlite3
    standard
    swoole
    sysvmsg
    sysvsem
    sysvshm
    tokenizer
    xml
    xmlreader
    xmlwriter
    xsl
    yac
    zlib
- pika3.3.6


### 启动教程
#### 目录
```
project/
│
├── php_73_api/
│   ├── index.php
│   └── public/
│
├── php_80_api/
│   ├── index.php
│   └── public
docker/
│
├── nginx/
│   ├── conf/
│   ├── logs/
│   └── Dockerfile
│
├── oprnresty/
│   ├── logs/
│   ├── nginx/
│   └── Dockerfile
├── php/
│   ├── php7.3.2/
|       ├── logs/
│       └── Dockerfile
│   └── php8.0.26/
|       ├── logs/
│       └── Dockerfile
├── pika/
│   ├── logs/
│   └── Dockefile
```

### 启动说明
> php_73_api nginx 配置
```nginx configuration
server {
    listen 8086;
    access_log  /www/wwwlogs/nginx/os_api.access.log;
    error_log  /www/wwwlogs/nginx/os_api.error.log;
    server_tokens           off;

    root /www/wwwroot/project/os_activity/public;

    index index.php;

    keepalive_timeout       600s;

    if (!-e $request_filename) {
        rewrite ^(.*)$ /index.php?s=/$1 last;
        break;
    }

    try_files $uri $uri/ /index.php?$query_string;

    add_header              Access-Control-Allow-Origin '*';
    add_header              Access-Control-Allow-Headers X-Requested-With;
    add_header              Access-Control-Allow-Methods GET,POST,OPTIONS;

    client_max_body_size 20M;
    client_body_buffer_size 128k;

    location ~\.php$ {
        fastcgi_pass php73_fpm; # 这里写php容器的container_name
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
    }
}
```
```bash
    cp docker-compose.yml.tmpl  docker-compose.yml
    docker-compose up -d
```

### 我的个人博客
[梦逸灵箭 https://www.program-er.com](https://www.program-er.com)
