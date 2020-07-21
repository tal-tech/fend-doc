# HTTP封装

此类内封装了关于HTTP的一些操作，如获取参数，发送Post、Get请求、跳转页面等(同Request函数)

### Post\Get\Put\Patch\Delete 请求
```php
<?php
use \Fend\Funcs\FendHttp;

//获取某个接口的返回json, 失败返回false
$result = FendHttp::getUrl("http://test.com/api/xx", 'search_keyword=yes&xxx=xxx', 'post', 1000, array("header1: xxx", "header2: xxx"));

//以下必须升级fend-core 插件 到最新版方可使用
$result = FendHttp::getUrl("http://test.com/api/xx", ["search_keyword" => "yes", "xxx" => "xxx"], 'post', 1000, array("header1" => 'xxx'));

$result = FendHttp::httpRequest("http://test.com/api/xx", "wd=cc", "GET", 8000,["test: 1"], ["option"=>[CURLOPT_HEADER => true]]);

$result = FendHttp::httpRequest("http://test.com/api/xx", ["wd" => "dd"], "post", 10000,["test" => 1]);

$result = FendHttp::getUrl("http://test.com/api/xx", 'search_keyword=yes', 'post', 1000, ['header: 111', 'header: 222']);

```

### 批量请求网址

```php
<?php
$urlList = [
            [
                "url" => "http://www.fend.com/",
                "method" => "post",
                "data" => ["search"=>"q"],
            ],
            [
                "url" => "http://www.fend1.com/",
                "method" => "post",
                "data" => ["search"=>"q"],
            ],
            [
                "url" => "http://www.fend02.com/",
                "method" => "post",
                "data" => ["search"=>"q"],
            ]
        ];
$result = FendHttp::getMulti($urlList);
```
