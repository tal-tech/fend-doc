# 分级日志

Fend\Logger类是此框架提供的分级日志，主要提供以下级别日志：

 * Debug Debug日志，详细信息，方便调试
 * Trace Trace日志，跟踪日志
 * Notice Notice日志，提示日志
 * Info Info日志，关键跳转，服务提示信息
 * Error 业务或框架底层错误日志，常见错误都会在此体现
 * Alarm 警报的日志，如一些已知场景应当预警的，可以输出此日志
 * Exception Exception异常日志

## 底层实现简介

Fend\Logger产生的日志，都会使用LogAgent类实现落地

LogAgent目前支持三种日志落地模式：

* Swoole多进程间日志队列及单进程日志落地
* 直写日志
* 日志缓存到内存在请求处理完毕后落地日志

解决的问题：fpm下多进程同时写同一个日志文件相互覆盖、性能瓶颈

### 日志配置

 > app\Config\Fend.php

```php
return [
    "log" => [
        "trace" => true, //是否开启trace日志
        "level" => \Fend\Logger::LOG_TYPE_INFO,     //分级日志记录级别，如果不想记录设置为LOG_TYPE_NONE
        "path"  => SYS_ROOTDIR . 'logs' . FD_DS,    //日志记录绝对路径 \Logs\eagleeye\*.log
        "filenameWithPid" => true,                  //日志文件增加getmypid，线上建议开启，主要原因是多进程高并发写大于8k的日志会导致文件内容相互覆盖
        "logFormat" => "json",                      //日志输出格式 json: json_encode方式, export:var_dump常见标准日志
        "logRoll" => "day",                         //日志滚动规则 none:不滚动，day:按天滚动，hour:按小时滚动
    ],

    "debug" => true,        //是否开启debug模式，如果开启在网址增加query参数?wxdebug=1可以看到错误原因

];
```

## 如何使用

```php
use \Fend\Logger;

//直接写入日志，用于特殊情况的临时日志文件输出
//若设置$debug=1会在返回内容或控制台直接输出内容
//file为文件名，生成文件会放在 logs/fend/内
Logger::write($msg, $file = "fend.log",$debug = 0);
Logger::write("you can got more", "user_error.log", 0);

//设置最低输出日志级别
//如设置成info，debug，notice日志将不会输出
Logger::setLogLevel($level);
Logger::setLogLevel(Fend_Log::LOG_TYPE_INFO);

//设置日志落地路径 会覆盖框架默认Fend.php的配置
LogAgent::setLogPath("/home/logs/");

//日志输出格式为json，可选export(var_dump方便debug)，会覆盖Fend.php默认日志配置
LogAgent::setFormat("json");

//日志文件名内含pid进程id，用于大日志append，原因如下章节，会覆盖Fend.php默认日志配置
LogAgent::setFileNameWithPid(true);

//日志滚动周期，hour 每小时 ，day 每天，none 不滚动，会覆盖Fend.php默认日志配置
LogAgent::setLogRoll("hour");

//日志文件前缀，根据需要定制，会覆盖Fend.php默认日志配置
LogAgent::setLogPrefix("fend");

//debug日志
//tag 模块名称，用来区分是哪个模块输出的日志
//msg 错误信息文本
//extra 附加用户记录数据,如果附加的数据在列表中会被作为独立key记录，如果是自定义key，会放到extra字段内

Logger::debug($msg, $tag, $extra = array());
Logger::debug("user get info by uid $uid not found!","user_info_getByUid", array("uid" => 123123));

//其他可选日志级别输出

Logger::trace
Logger::notice
Logger::info 
Logger::error
Logger::alarm

//exception异常日志记录
//tag 模块名称，用来区分是哪个模块输出的日志
//file 输出这条日志的php文件名
//line 哪一行产生的日志
//msg 错误信息文本
//code exception code
//backtrace 错误堆栈
//extra 附加用户记录数据

Fend\Logger::exception($tag, $file, $line, $msg, $code = "", $backtrace = "", $extra = array())；
```

Json格式日志查看问题

```bash
#所有生成的日志都会放在框架目录内logs/eagleeye 日志内容为json，如需肉眼查看可以安装jq插件

yum install jq
tail -f /eagleeye/20180212.log | jq .

#即可实时查看
#建议使用ELK将此日志倒入，即可分析归类查看（方便按列统计分析）
#开发环境可以将配置logFormat设置为export（var_dump格式输出）
```

## 超长日志append原子性问题
目前线上日志，如果长度超过指定长度，append不是原子性，所以一个进程一个文件，非高并发下无所谓