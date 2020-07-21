# 协程

## 协程简介

协程可以提高PHP的并行处理能力，能够充分利用阻塞IO的等待期处理其他请求逻辑。

### 切换称协程后的变化
 * global static 变量已经不再是 `当前请求内有效` 而是`多个请求内共享有效`，如果想限制回`当前请求级别内有效`请使用`RequestContext`类统一管理。
 * DI内用户注入的对象及数据是 `多个请求内共享有效`，除此以外DI内 `Request`、`Response` 为 `当前请求级别共享内有效`
 * Mysql、Redis等连接对象之前是`单例`，现由`连接池`进行统一管理发放，而数据连接只支持`本协程内`单例，这种`协程内有效`对象及变量，可用`Context`类统一管理
 * `阻塞等待IO操作`现在可以使用`Coroutine` 创建子协程`并行`执行（如网络请求，磁盘操作）
 * `FPM`用户切成协程时需要注意`变量作用域`，已经改为协程的代码，可在FPM模式下正常运行（`defer除外`）
 * 使用Swoole阻塞模式时Worker往往开很大(200+)、使用协程后可以降低worker个数(20+)，请根据`压测效果`来决定。
 
## 环境依赖

* PHP >= 7.2
* Swoole PHP 扩展 >= 4.4

## fend-plugin-server 开启协程

在 `app/Config/Swoole.php` 中加入以下配置即可开启协程：

```php
return [
    'swoole'    => [
        // 其他配置省略
        "enable_coroutine" => true, // 是否开启swoole协程  警告，仅允许在swoole 4.4以上版本开启
    ]
]

```
协程如果开启成功，在服务用命令行启动时会提示
```bash
loading special config:./app/Config/Swoole.php
start:fend
start coroutine...success #协程版本提示
```
 
## 协程组件引入
目前支持开启协程功能的组件：

* fend-core
* fend-plugin-redis
* fend-plugin-db
* fend-plugin-mysqli
* fend-plugin-pdo
* fend-plugin-server
* fend-plugin-cache

以上组件是实际协程有更改的组件，要想切换协程。请将 composer.json 内以fend开头的所有组件版本设置为~1.3.0 即可 

注意：目前已知`memcached`暂不支持协程，目前还未提供其连接池，如有需要后续提供，memcached不支持hook会导致使用时阻塞等待其返回

## 1.2.* 与 1.3.* 版本兼容问题
使用协程1.3版本开发的业务代码，可运行在fpm及swoole阻塞模式下（注意:不支持defer）
 
1.2版本升级成1.3需要对业务内static变量使用范围合理性排查一次 

## 其他注意事项
 * 协程版建议根据压测结果来决定，服务的一些配置，如worker数
 * php.ini 内 memory_limit 建议开更大 具体根据情况处理
 * 业务代码中建议使用框架封装函数创建协程，自行原生创建context无法继承变量setGlobal内内容及链路跟踪