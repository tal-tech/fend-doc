# 连接池

在协程模式下，同一个进程内可能会有多个请求在同时处理，也就可能同时发起多个SQL或者Redis请求，因此每个协程都需要有一个自己独享的连接来发起请求。因此需要有连接池来维护多个连接，保证每个协程使用的连接是确认的

## 支持连接池的组件

以下组件内部使用连接池：

* fend-plugin-cache
* fend-plugin-redis
* fend-plugin-redissdk
* fend-plugin-db
* fend-plugin-pdo
* fend-plugin-mysql

## 连接池配置

在 `Db` 或 `Redis` 原有的配置中增加如下配置：

```php
'default' => array(
    // 原有配置内容省略
    'pool' => [
        'max_connections' => 20,        // 连接池最大连接数
        'wait_timeout' => 3.0,          // 等待空闲连接最长时间 
    ]
),

```

配置后，原有的调用方式不变，底层会自动切换到连接池模式（仅限协程下使用）

## 协程内单例实现
 * 每个协程内拥有一个Context，本协程内有效
 * 每次请求数据层底层时，检测当前context内是否已经拥有一个可用对象
 * 没有则从连接池取出一个链接对象
 * 并存储到context内、设置defer()回收