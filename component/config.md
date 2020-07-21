# 配置管理

经过长时间讨论及参考，Fend对Config做了一些特性
 * 建议所有配置都放在\app\Config\内
 * 按需加载，配置加载时指定配置文件名称，大小写敏感
 * 一个类型配置统一放在一个文件内，并使用return返回数组，杜绝内部有php代码
 * 配置只会加载一次，加载后会在内存中缓存
 * 业务配置也可以放在配置文件夹内，但是不建议过多、太复杂
 * 多组配置可以在\app\Config内创建子文件夹，如:online\test\dev 发布版本后用脚本cp \app\Config\Online\* \app\Config\
 
## 获取配置
```php
use Fend\Config;

//获取Db配置全部内容
$dbConfig = Config::get("Db");

//获取默认配置
$config = $dbConfig["default"];
```

## 配置 点分符 检索
新版本的Config目前支持`点分符`，对比上一个示例  
我们可以使用如下例子写法直接获取到配置内某个下标内容 
支持多层级 

```php
use Fend\Config;

//获取Db配置指定配置内default数组下标内内容
//如果文件不存在或选项不存在会抛异常

try {
  $dbConfig = Config::get("Db.default");
} cache(\Exception $e) {
   
}

//默认值方式获取配置
//如果指定默认值，那么如果配置不存在不会抛出异常会返回默认值

$config = Config::get("Db.Default", ["host"=>"127.0.0.1","port"=> 3306]);

```