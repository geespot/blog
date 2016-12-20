---
title: nignx_ssl
date: 2016-12-07 10:06:43
tags:
- 技术
categories: [[linux,数据库,运维]]
---
### nginx 私有证书生成
```bash
$ cd /etc/nginx/conf.d
$ openssl genrsa -des3 -out server.key 1024
$ openssl req -new -key server.key -out server.csr
$ cp server.key server.key.org
$ openssl rsa -in server.key.org -out server.key
$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```   

### nginx 配置
```conf
server {
    server_name ss.shgt.com;
    listen 443;
    ssl on;
    ssl_certificate /etc/nginx/conf.d/server.crt;
    ssl_certificate_key /etc/nginx/conf.d/server.key;
    location / {
        root   /usr/share/nginx/html;
        index  index.php index.html index.htm;
    }

}
```   
