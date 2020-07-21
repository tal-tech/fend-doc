# 依赖注入
依赖注入用途：
 * 能够将业务代码内服务或类之间硬代码的依赖关系脱耦合，通过控制反转对业务代码进行隔离，隔离后代码依赖是中心式而不是瀑布式依赖关系（各有好坏） 
 * 用户可以直接通过DI获取到对象，无需手工new对象，降低频繁GC
 * 对散乱在外的对象进行统一管理
 * 隔离硬代码实现的依赖
 * 跨代码层级传递对象（不推荐）

缺点：全框架完全围绕DI进行工作，会导致DI十分庞大

Fend 目前有两种方式全局传递数据，不过从以往经验来看不推荐业务使用全局变量传递数据 

目前Fend没有使用集中式DI管理，目前是分散式 如fend-plugin-db fend-plugin-cache是两个管理类，具体工厂出来的类是fend-plugin-mysqi,fend-plugin-pdo,fend-plugin-memcache,fend-plugin-redis

## Fend\Di
\Fend\DI::Factory-&gt;set\("name", $var\);
同常见DI一样，直接set get即可设置获取，不推荐大规模业务使用此功能作为数据全局共享。

## Di的使用

### set

`\Fend\DI::Factory()->set(string $key, $value)` 方法用于向Di中注入一个值`$value`，并为其指定键值为`$key`

### get

`\Fend\DI::Factory()->get(string $key, $params = null)` 方法。

* 如果传入的 `$key` 是一个普通字符串，则尝试通过$key值去检索是否有对应的值，并返回。

```php

class User
{
    public $name;
    
    public function __contruct(string $name)
    {
        $this->name = $name;
    }
}

class Teacher
{
    public static function factory(string $name)
    {
        return new self($name);
    }

    public $name;
    
    public function __contruct(string $name)
    {
        $this->name = $name;
    }
}


```


### make

`\Fend\DI::Factory()->make(string $key, $params = null)` 方法。

如果传入的 `$key` 是一个普通字符串，则直接返回 `$params` 。
如果传入的 `$key` 是一个完整的class，则底层会自动调用该类的构造函数创建一个新的对象，并使用$params作为其构造参数。

```php

class User
{
    public function __contruct(string $name, int $sex)
    {

    }
}

$user =\Fend\DI::Factory()->make(User::class, ['hello', 1]);

```