---
title: Nginx配置---同一端口下部署不同项目
date: 2020-12-12 19:43:14
summary: Nginx配置实现同一端口下部署不同项目，通过路由区别访问。
tags:
- Nginx
- Vue
- React
categories:
- [项目开发]
---

### 一、功能描述
- 实现： 在同一端口下部署一个react项目和一个vue项目，通过访问`/`或`/home`来访问vue项目，通过访问`/portfolio`路径来访问react项目。
- 版本： `nginx/1.16.1`、`react/16.13.1`、`react-router-dom/5.2.0`、`vue/2.6.11`、`vue-router/3.4.9`。

### 二、代码实现
#### 1.react项目配置
1. `package.json`中增加配置`homepage`字段，以域名`www.abc.cn`为例:

``` json
{
  "homepage": "https://www.abc.cn/portfolio"
}
```

2. `BroserRouter`中配置`basename`属性

``` js
// 其他引入项省略
// 引入路由组件
import {
    BrowserRouter as Router,
    Switch,
    Route
} from 'react-router-dom';

const App = () => {
    return (
        // 增加basename属性
        <Router basename="/portfolio">
            <div className={styles.app}>
                <LeftNav />
                <div className={styles.content}>
                    <Switch>
                        <Route path="/:routerid" exact>
                            <DemoList />
                        </Route>
                        <Route path="/:routerid/:demo" exact>
                            <DemoDetail />
                        </Route>
                        <Route path="/" exact>
                            <Home />
                        </Route>
                    </Switch>
                    <CopyRight />
                </div>
            </div>
        </Router>
    )
}

export default App
```

#### 2.vue项目配置
1. `router`中配置基础路径

``` js
const router = new VueRouter({
  mode: 'history',
  base: '/home', //基础路径
  routes
});
```
2. `vue.config.js`（项目中没有该文件的，可以在项目根目录下创建）中配置基础路径

```js
module.exports = {
  publicPath: process.env.NODE_ENV === 'production'
    ? '/home'
    : '/',
    // 其他配置内容省略
}
```

#### 3.Nginx配置
`nginx.conf`中相关配置（我的nginx页面根目录为`/data/www`，vue项目部署在`/data/www/home`目录下，react项目部署在`/data/www/portfolio`目录下）
``` bash
    server {
        listen       80;
        server_name  <your_server_name>;
        root /data/www;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        # http to https
        return 301 https://$host$request_uri;

        # /重定向到/home
        location / {
                rewrite / /home permanent;
        }

        location /home {
                index index.html index.htm;
                try_files $uri $uri/ /home/index.html;
        }

        location /portfolio {
                index index.html index.htm;
                try_files $uri $uri/ /portfolio/index.html;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
    
    # 如果没有配置ssl或者不使用https可以忽略下列配置
    server {
        listen       443 ssl;
        server_name  <your_server_name>;
        root         /data/www;

        # ssl证书相关配置
        ssl_certificate "/etc/nginx/cert/server.crt";
        ssl_certificate_key "/etc/nginx/cert/server.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
                rewrite / /home permanent;
        }

        location /home {
                index index.html index.htm;
                try_files $uri $uri/ /home/inex.html;
        }

        location /portfolio {
                index index.html index.htm;
                try_files $uri $uri/ /portfolio/index.html;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
```

### 三、问题解决
如上将两个项目部署在二级目录后，react项目下的子路由可以正常访问，且刷新访问也正常。vue项目的子路由通过`<router-link></router-link>`组件可以正常访问，但是刷新后会报“500 Internal Server Error”，可以通过下面方式解决。
修改nginx.conf中部分配置： 
```bash
        location /home {
                index index.html index.htm;
                try_files $uri $uri/ @router;
        }
        
        location @router {
                rewrite ^.*$ /home/index.html last;
        }
```
