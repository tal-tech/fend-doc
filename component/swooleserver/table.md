# Swoole Table
 swoole下可以在进程之间共享kv数据，通过这个方式可以降低外置资源压力，提高本地服务性能 
  
 注意频繁更新数据不建议放到这里，建议能够接受延迟的数据放到这里 
 
### 配置
 app\Config\Swoole.php
```php

    //共享swoole table
    "table" => array(
        "share" => array(
            "column"      => array(
                array("key" => "time", "type" => "int", "len" => 4),
                array("key" => "data", "type" => "string", "len" => 1024),
            ),
            "size"       => 1024, //最大保存多少条，建议超过实际个数
            "proportion" => 0.2, //hash预留空间比例 https://wiki.swoole.com/wiki/page/254.html
            "dumpfile" => SYS_ROOTDIR . "share.dump", //落地磁盘路径，设置会flash到磁盘，重启会加载
        ),
    ),
```
### Demo
```php
$table = Di::factory()->getTable("share");
if($table) {
    $table->set("test",["time" => time(), "data" => "123"]);
    var_dump($table->get("test"));
}

```