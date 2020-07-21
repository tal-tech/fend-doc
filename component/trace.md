# 分布式链路跟踪

Fend框架已经集成了 链路跟踪，用户仅需将其框架产生的日志导入ELK即可使用 

可通过Kinbana及LogCenter查看分析接口(code\响应性能\QPS\用户)情况 

### 链路跟踪记录范围
 > * FendHttp 对外请求及返回值记录
 > * Fend\Db\Mysql.php 及 Fend\Db\MysqlPDO.php的链接，查询记录
 > * 服务被请求历史及返回内容状态
 > * 框架异常Exception
 > * 分级日志

### 链路跟踪开关（Fend日志配置）

 > 配置文件路径 app\Config\Fend.php

```php
return [
    "log" => [
        "trace" => true, //是否开启trace日志
        "level" => \Fend\Logger::LOG_TYPE_INFO, //分级日志记录级别，如果不想记录设置为LOG_TYPE_NONE
        "path"  => SYS_ROOTDIR . 'logs' . FD_DS,    //日志记录绝对路径 \Logs\eagleeye\*.log
        "filenameWithPid" => true, //日志文件增加getmypid，线上建议开启，主要原因是多进程高并发写大于8k的日志会导致文件内容相互覆盖
        "logFormat" => "json", //日志输出格式 json: json_encode方式, export:var_dump常见标准日志
        "logRoll" => "day", //日志滚动规则 none:不滚动，day:按天滚动，hour:按小时滚动
    ],

    "debug" => true, //是否开启debug模式，如果开启在网址增加query参数?wxdebug=1可以看到错误原因

];
```
