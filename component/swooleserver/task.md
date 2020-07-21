# 异步任务

目前利用Swoole task实现了异步执行controller功能，可以并行执行一些异步任务。

```php
//投递任务给一个类
$result = \Fend\Task::Factory()->add("\App\Http\Index", $func ="index", $argv=array());
//如果产生异常会返回[code ,msg]
var_dump($result);
```