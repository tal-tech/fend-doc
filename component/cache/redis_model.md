# Fend Redis Model组件

`fend-plugin-redismodel`组件提供以下两种功能：

* Redis key的统一管理
* 针对Redis操作的常用封装

## 配置

首先需要在项目的 `app\Config\Redis.php` 配置文件中，加入以下配置：

```php

return [
    // 指定Redis Key的配置文件的路径
    'key_map_path' => SYS_ROOTDIR . 'app/Config/RedisKey',
]

```

然后可以在指定的路径下，创建对应的配置文件。例如：

```php

<?php
// Test/Test.php


return [

    'test_key_1' => [
        'key' => 'test_string_key_%d',      // key的字符串
        'params' => [                       // key的参数
            'id',
        ],
        'type' => 'string',                 // redis 存储类型
        'instance' => 'default'             // redis 配置实例
    ],

    'test_key_2' => [
        'key' => 'test_set_key_%d_%d',      // key的字符串
        'params' => [                       // key的参数
            'id',
            'index'
        ],
        'type' => 'zset',                   // redis 存储类型
        'instance' => 'default'             // redis 配置实例
    ],

    'test_key_3' => [
        'key' => 'test_hash_key_%d',        // key的字符串
        'params' => [                       // key的参数
            'id',
        ],
        'type' => 'hash',                   // redis 存储类型
        'field_list'    => [                // hash table 的子字段列表
            'name',
            'mobile',
        ],
        'instance' => 'default'             // redis 配置实例
    ],
];

```

这里， `test_key_1` 是用于索引对应的配置，实际生成的key是基于配置中的`key`的值进行拼接。

参与拼接的参数通过`params`数组进行定义，在调用时需要保证传入参数跟定义一一对应，否则会抛出异常报错。

`type` 字段定义了当前key的存储值类型，具体的类型如下：

'string', 'incr', 'hash', 'zset', 'list', 'set'

| type | 类型 |
| --- | --- |
| string | 字符串类型， 会自动进行json转义 |
| incr | 自增类型 |
| hash | hash table类型 |
| zset | 有序set |
| list | list类型 |
| set | set类型 |

`instance` 定义了当前key使用了哪一个redis配置实例。

`field_list` 仅用于 hash 类型，用于规定hash table的子域的键名。

## Model定义

以下是一个测试Model的定义：

```php

class TestModel extends RedisModel
{
    protected $configName = 'Test.Test';            // 配置文件名称 对应指定的key文件目录里的Test目录下的Test.php文件

    protected $dbType = Cache::CACHE_TYPE_REDIS;    // 驱动方式，详见Cache组件，默认普通Redis驱动
}
```

所有Model均支持单例调用，因此可以在代码中通过以下代码获取：

```php
$model = TestModel::factory();
```

## 使用

当我们需要访问一个key时，可以在model中编写一个函数，如下：

```php

    public function testGet(int $id)
    {
        $result = $this->get('test_key_1', [
            'id'    => $id
        ]);
        return $result;
    }

```

部分操作支持传入多个key，例如：

```php

    public function testMGet(array $ids)
    {
        $list = [];
        foreach ($ids as $id) {
            $list[] = [
                'id'    => $id,
            ];
        }

        $result = $this->get('test_key_1', $list);
        return $result;
    }

```