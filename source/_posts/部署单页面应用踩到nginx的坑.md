title: 部署单页面应用踩到nginx的坑
date: 2016-08-24 09:58:33
tags:
  - nginx  
  - 运维日记  
---

### 最近在公司里配置一个单页面应用遇到的问题。

需求：由于单页面应用使用js做路由，并且还需要从Rails应用的接口里拿数据，因此需要把单页面应用的路由和Rails的路由分开做代理。

场景：SPA静态HTML在Rails的public目录下。需要配置Nginx把请求转发到SPA的静态文件。同时还需要把api请求代理到Rails应用里。


研究发现可以通过使用try_files指令来实现SPA静态文件的代理。

在测试服上可以工作的配置如下：
```nginx
upstream app_name {
  server <app_ipaddr>;
}

server {
  listen 80 default deferred;
  server_name domain;
  
  location / {
    try_files $uri $uri/ /index.html;
    proxy_pass http://app_name;
    proxy_redirect off;
  }

  location ~ ^/(api/v1|api/auth/v1)/ {
    proxy_pass http://app_name;
    proxy_set_header Accept "application/vnd.example.v1";
    proxy_redirect off;
  }

  location ~ ^/(api/v2|api/auth/v2)/ {
    proxy_pass http://app_name;
    proxy_set_header Accept "application/vnd.example.v2";
    proxy_redirect off;
  }
}
```
同样地配置在生产环境的Nginx中try_files报了如下的错误
```nginx
 rewrite or internal redirection cycle while internally redirecting to "/index.html"
```
初步判断以为是vps的防火墙ban了nginx的代理请求。经过排查发现，是生产环境的nginx因为版本比较高的原因，try_files并没有通过proxy_pass 而只是在本地try_files了，因此会找不到文件报错

猜测nginx的try_files实际上会在nginx上寻找try_files参数里的location，当找不到的时候会去寻找本地的文件。如果仍然找不到的话那就会出现之前的报错。
加上location之后发现这个try_files是会找到location并且根据location里的proxy_pass代理拿到try_files的'/index.html'。
```nginx
upstream app_name {
  server <app_ipaddr>;
}

server {
  listen 80 default deferred;
  server_name domain;
  
  location /index.html {
    proxy_pass http://app_name;
    proxy_redirect off;
  }

  location / {
    try_files $uri $uri/ /index.html;
    proxy_pass http://app_name;
  }

  location ~ ^/(api/v1|api/auth/v1)/ {
    proxy_pass http://app_name;
    proxy_set_header Accept "application/vnd.example.v1";
    proxy_redirect off;
  }

  location ~ ^/(api/v2|api/auth/v2)/ {
    proxy_pass http://app_name;
    proxy_set_header Accept "application/vnd.example.v2";
    proxy_redirect off;
  }
}
```