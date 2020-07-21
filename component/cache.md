# 数据缓存管理组件
fend-plugin-cache组件，提供对内存Cache组件的实例统一管理 

统一封装cache组件，利用单例工厂模式对底层Driver进行统一创建管理

## Cache工厂使用例子

```php
$redis = \Fend\Cache::factory(\Fend\Cache::CACHE_TYPE_REDIS, "配置项名称");
$redis->set("test", "yes");
echo $redis->get("test", "yes");
```