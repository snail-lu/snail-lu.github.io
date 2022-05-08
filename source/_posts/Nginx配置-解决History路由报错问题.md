---
title: Nginx配置---解决History路由报错问题
date: 2020-12-11 23:55:38
summary: Vue项目中history路由刷新报错修复。
tags:
- Vue
- Nginx
- History
categories:
- [项目开发]
---

### 一、功能描述
- **实现**：
    -  vue项目中使用`history`模式的路由时，解决访问深层级的路由返回404的问题。
    -  nginx配置了ssl证书的情况下，解决`history`路由刷新报404的问题。
- **版本**：nginx/1.16.1。

### 二、代码实现
`nginx.conf`中相关配置：

``` bash
    server {
        listen       80;
        server_name  <your-server-name>;

        # http 转成 https，配置了ssl证书时启用
        return 301 https://$host$request_uri;

        # 解决history路由刷新问题
        location / {
            # index.html文件在服务器中的存储目录
            root /data/www;  # /data/www需要修改为你服务器中的目录
            index index.html index.htm;
            
            #资源访问失败后定向到index.html
            try_files $uri $uri/ /index.html;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
    
    # SSL证书配置
    server {
        listen       443 ssl;
        server_name  <your-server-name>;

        ssl_certificate <your_ssl_certificate_filepath>;
        ssl_certificate_key <your_ssl_certificate_key_filepath>;
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;

        # 解决http转https后路由报错问题
        location / {
            root /data/www;
            index index.html index.htm;
            try_files $uri $uri/ /index.html;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
```