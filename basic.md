# 安装

## 软件环境

PHP 7.1+

Swoole 1.10 + 或 4.4.x (请勿使用2.x版本)

## PHP扩展依赖

 > * pcntl
 > * mysqli
 > * mysqlnd
 > * pdo\_mysql
 > * memcached
 > * mbstring
 > * json
 > * curl (libcurl 7.29+)
 > * bcmath
 > * redis
 > * gd

## 硬件及系统要求

* CPU 4核+
* 内存 8G+
* 支持Centos、Ubuntu、Debian. windows 及 MacOS仅限于测试

## 框架模式简介

框架支持两种模式运行：

* PHP-FPM：对服务稳定性要求高，但对性能要求不高的项目建议使用
* Swoole服务容器：对服务性能及服务治理有要求的项目建议使用

Fend使用十分简单，拷贝到 LNMP/Lamp/XAMPP 环境即直接使用（Swoole模式需要扩展）

## 框架安装
clone下来直接跑


## PHP-FPM模式 Nginx配置
 * 将Fend框架代码下载到部署目录
 * 配置nginx指向请求www\index.php即可
 * nginx配置请参考如下:
 
```nginx
server {
        listen 80;
        server_name     www.fend.com www.fend1.com;
        access_log      /tmp/www.fend.acc.log;
        error_log      /tmp/www.fend.err.log;
#        root           /Users/xcl/Code/fend/www;
        root            /Users/xcl/Code/fend-project/fend-demo/www;
        index   index.php index.html index.htm;
        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                #fastcgi_pass unix:/var/run/php7.0-fpm.sock;
                fastcgi_index  index.php;
                root            /Users/xcl/Code/fend-project/fend-demo/www;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}
```

## Swoole模式 Nginx代理配置
 * Swoole模式可以不使用Nginx模式
 * 如果需要nginx代理可以参考如下配置
 
```nginx
server {
    root /data/fend/www/;
    server_name www.fend.com;

    location / {
        proxy_http_version 1.1;
        proxy_set_header Connection "keep-alive";
        proxy_set_header X-Real-IP $remote_addr;
        if (!-e $request_filename) {
             proxy_pass http://127.0.0.1:9501;
        }
    }
}
```

## Linux内核调优
Linux服务器，只有对内核做一定优化后才能有更好的性能表现，建议边压测边调整
 
Swoole官方内核优化文档：[https://wiki.swoole.com/wiki/page/11.html](https://wiki.swoole.com/wiki/page/11.html) 

