---
title: Nginx配置---启用gzip压缩
date: 2020-12-13 21:17:32
summary: Nginx配置开启Gzip压缩功能， 可以使网站的css、js 、xml、html 等文件在传输时进行压缩，提高访问速度。
tags:
- Nginx
- Gzip
categories:
- [随笔]
- [前端]
- [开发配置]
---

### 一、开发环境
- 版本： nginx/1.16.1。

### 二、配置实现
参数详解：

``` bash
# 开启或关闭gzip模块（实时压缩输出）
gzip on|off;     

# 开启或关闭gzip静态文件处理模块（提前压缩，需要nginx中添加上http_gzip_static_module模块）
gzip_static on|off;

# 设置压缩缓冲区大小，例如"gizp_buffers 4 8k"就是设置为4个8k内存作为压缩结果数据流缓存
gzip_buffers number size;      

# 设置允许压缩的页面最小字节数; 页面字节数从header头中的Content-Length中进行获取，建议设置成大于1k，小于1k没有压缩的必要. 
gzip_min_length length;         

# 识别http的协议版本。可以不用配置。
gzip_http_version 1.0|1.1;

# 设置压缩比，可以是0到9中的任一个，级别越高，压缩结果就越小，但同时消耗CPU资源越高，一般折中取6
gzip_comp_level [1-9];

# 设置需要压缩的MIME类型，如果不在设置类型范围内的请求不进行压缩，线上配置时尽可能配置多的压缩类型。
gzip types mime-type [mime-type ...];

# Nginx作为反向代理的时候启用，开启或者关闭后端服务器返回的结果，匹配的前提是后端服务器必须要返回包含"Via"的 header头。
gzip_proxied [off|expired|no-cache|no-store|private|no_last_modified|no_etag|auth|any]

# 配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
gzip_disable "MSIE [1-6]\.";

# 增加vary头
gzip_vary on;
```
线上配置：

``` bash
http {
    ...
    
    # gizp压缩配置
    gzip on;
    gzip_min_length 1k;
    gzip_buffers 4 8k;
    gzip_http_version 1.1;
    gzip_comp_level 6;
    gzip_types text/plain qpplication/x-javascript text/css application/xml text/javascript application/javascript application/json;
    gzip_disable "MSIE [1-6]";
    gzip_vary on;
    
    ...
}

```

### 三、最终效果
未启用gzip时，网站资源访问速度：
![image](https://note.youdao.com/yws/api/personal/file/WEBcfa2e5ef98f92e47587cb10bcd36a65d?method=download&shareKey=bdeff67369d6b02e4604fa7c2e670e55)

启用gizp后：  
![image](https://note.youdao.com/yws/api/personal/file/WEB93a8f32075283b4c36ec56a8dfd1c92c?method=download&shareKey=5e9be38f76768495e915a391db5e2038)

### 四、参考文档
[1.Nginx中文文档--HttpGzip模块](https://www.nginx.cn/doc/standard/httpgzip.html)  
[2.Nginx性能优化功能- Gzip压缩(大幅度提高页面加载速度)](https://cloud.tencent.com/developer/article/1374023)