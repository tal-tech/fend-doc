# Fend ElasticSearch组件

## 功能说明
封装自composer elasticsearch/elasticsearch组件，只是提供更友好的代码提示和封装，目前支持6.x版本

## 安装

```bash
#注意 需要根据es服务器版本设置这个composer版本
composer require elasticsearch/elasticsearch ～6.5.0
```

## 配置
```php
<?php
return [
    //默认es配置
    "default" => [
        "hosts" => [
            "10.90.71.158:9200",
        ]
    ],
    //其他es配置，支持密码验证等
    "fend_demo" => [
        "hosts" => [
            [
                'host' => 'foo.com',
                'port' => '9200',
                'scheme' => 'https',
                'path' => '/elastic',
                'user' => 'username',
                'pass' => 'password!#$?*abc'
            ],
            [
                'host' => 'localhost',    // Only host is required
            ]
        ]
    ],
];
```
## ElasticSearch 7.x版本兼容
7.x版本没有type，使用时牵扯到type设置数组、删除即可 
另外 需要设置type的地方可以设置为_doc 即可兼容 
 
## 使用演示

```php
<?php

use Fend\ElasticSearch\ElasticSearch6;

include "init.php";

$es = ElasticSearch6::Factory("default");

$setting = [
    'number_of_shards' => 3,
    'number_of_replicas' => 2
];

$mapping = [
    "testdoc" => [ //es 7.x版本没有这层结构
        '_source' => [
            'enabled' => true
        ],
        'properties' => [
            'first_name' => [
                'type' => 'keyword',
            ],
            'age' => [
                'type' => 'integer'
            ]
        ]
    ]
];

///////////////////////////
//create index
///////////////////////////

try {
    $ret = $es->createIndex("test", $mapping, $setting);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("创建索引", $ret);

///////////////////////////
//index doc auto incr id
///////////////////////////

$id = "";
try {
    $id = $es->indexDocument("test", "testdoc", ["first_name" => "yes", "age" => 12]);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("索引数据，自动id生成", $ret);

///////////////////////////
//index doc with set id
///////////////////////////

try {
    $ret = $es->indexDocumentWithId("test", "testdoc", "testid", ["first_name" => "yes", "age" => 12]);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("索引数据，指定id", $ret);

///////////////////////////
//buck index doc
///////////////////////////

$data = [
    [
        "id" => 47,
        "type" => "testdoc",
        "index" => "test",
        "data" => [
            "first_name" => "so yes day",
            "age" => 22,
            "@timestamp" => time(),
        ],
    ],
    [
        "type" => "testdoc",
        "index" => "test",
        "data" => [
            "first_name" => "so yes day 2",
            "age" => 32,
            "@timestamp" => time(),
        ],
    ],
    [
        "type" => "testdoc",
        "index" => "test",
        "data" => [
            "first_name" => "so yes day 3",
            "age" => 44,
            "@timestamp" => time(),
        ],
    ],
    [
        "type" => "testdoc",
        "index" => "test",
        "data" => [
            "first_name" => "so yes day 4",
            "age" => 44,
            "@timestamp" => time(),
        ],
    ],
];
try {
    $ret = $es->bulkIndexDocument($data);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("批量索引数据", $ret);

///////////////////////////
//根据id获取文档内容
///////////////////////////

$doc = "";

try {
    $doc = $es->getDocumentByid("test", "testdoc", 47);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("获取文档索引信息", $doc);

///////////////////////////
//更新索引 by id
///////////////////////////

try {
    $doc = $es->updateDocumentById("test", "testdoc", 47,["age"=> 99]);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("更新索引", $doc);

///////////////////////////
// fetch doc info by id
///////////////////////////

try {
    $doc = $es->getDocumentByid("test", "testdoc", 47);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("获取文档索引信息", $doc);

///////////////////////////
// delete doc by id
///////////////////////////

try {
    $doc = $es->delDocumentById("test", "testdoc", $id);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("删除指定id文档", $doc);


///////////////////////////
//search
///////////////////////////

$search = [
    'query' => array(
        'bool' => array(
            'must'     => array(
                array(
                    'query_string' => array(
                        'query'            => "yes",
                        'analyze_wildcard' => true,
                        'default_field'    => '*',
                    ),
                ),
            ),
        ),
    ),
    'size'  => 20,

];
try {
    $ret = $es->search("test", "testdoc", $search);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("查询结果", $ret);

///////////////////////////
//search by scroll
///////////////////////////

try {
    $ret = $es->searchByScroll("test", "testdoc", $search,20,1000);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("查询结果scroll", $ret);

///////////////////////////
//get index setting
///////////////////////////

try {
    $ret = $es->getIndexSetting("test");
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("获取索引设置", $ret);

///////////////////////////
//set setting of index
///////////////////////////

$setting = [
    'number_of_replicas' => 5,
    'refresh_interval' => -1
];

try {
    $ret = $es->setIndexSetting("test", $setting);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("设置索引配置", $ret);


///////////////////////////
//set index mapping
///////////////////////////
$mapping = [
    'sex' => [
        'type' => 'integer'
    ]
];
try {
    $response = $es->putMapping("test", "testdoc", $mapping);
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("获取索引字段映射设置", $response);


///////////////////////////
//get index mapping
///////////////////////////

try {
    $response = $es->getMapping("test");
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("获取索引字段映射设置", $response);


///////////////////////////
//remove index
///////////////////////////

try {
    $ret = $es->removeIndex("test");
} catch (\Exception $e) {
    var_dump("error",$e->getMessage(), $e->getCode());
}
var_dump("删除索引", $ret);


```