# Memcache驱动
目前Fend框架使用的是Memcached扩展

### 调用样例

```php
$memcache = \Fend\Cache::Factory(Fend\Cache::CACHE_TYPE_MEMCACHE, "配置名称");
$memcache->set("test", "yes");
echo $memcache->get("test");
```

### 小技巧
```php
//查询cache，若cache不存在调用回调填充数据并返回结果 by @夏磊
$data = $memcache->getOrSet('key', function() {
   // 查询数据库或其他操作...
   return ['userId' => 1];
}, 3600);
var_dump($data); // ['userId' => 1]
```

### 配置

> 配置路径: /app/Config/Memcached.php

```php
return array(
    'default' => [
        'hosts' => [
            ['host' => '127.0.0.1', 'port' => 11211, 'weight' => 100],
        ],
        'persistent_id' => '', //设置就会开启长链接
        'pack_type' => \Fend\Cache\Memcache::PACK_TYPE_JSON, //默认json序列化
        'prefix' => '' //key前缀
    ],
    'memcache_broken' => [
        'hosts' => [
            ['host' => '127.0.0.2', 'port' => 11211, 'weight' => 100],
        ],
        'persistent_id' => '',
        'pack_type' => \Fend\Cache\Memcache::PACK_TYPE_SERIALIZE,
        'prefix' => 'ts_'
    ],
    'memcache_more' => [
        'hosts' => [
            ['host' => '127.0.0.1', 'port' => 11211, 'weight' => 100],
            ['host' => '127.0.0.1', 'port' => 11211, 'weight' => 50],
        ],
        'persistent_id' => '',
        'pack_type' => \Fend\Cache\Memcache::PACK_TYPE_JSON,
        'prefix' => 'ts_'
    ]
);
```

### 其他细节

* 使用memcached扩展实现
* 长连接
* 配置如果存在子数组，那么会自动做分片hash

### 版本建议

很多操作系统的libmemcached版本偏低，会导致一些情况下异常。

如果碰到类似情况 请上https://launchpad.net/libmemcached/+download 更新到1.0.18版本

