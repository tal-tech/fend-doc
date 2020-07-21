# 异常处理
Fend最新版本支持ExceptionHandle可配置，用户可以对最终Exception拦截做个性化处理

Fend默认的异常处理会在fend-core\src\ExceptionHandle，需要改变默认行为时可通过配置改成用户自定义类去处理

## 配置
路径：app\Config\Fend.php 配置项：exceptionHandle
```php
return [
    "log" => [
        "trace" => true, //是否开启trace日志
        "traceResponseMaxLen" => -1, //-1 记录全部(默认) 0时不记录 >0超过这个长度截断
        "level" => \Fend\Logger::LOG_TYPE_INFO, //分级日志记录级别
        "path" => SYS_ROOTDIR . 'logs' . FD_DS, //日志文件保存路径
        "filenameWithPid" => true, //日志文件增加getmypid，线上建议开启，主要原因是多进程高并发写大于8k的日志会导致文件内容相互覆盖
        "logFormat" => "json", //日志输出格式 json: json_encode方式, export:var_dump常见标准日志
        "logRoll" => "hour", //日志滚动规则 none:不滚动，day:按天滚动，hour:按小时滚动
        "logPrefix" => "fend", //日志文件前缀，用于区分多个项目日志
    ],

    "debug" => true, //是否开启debug模式，如果开启在网址增加query参数?wxdebug=1可以看到错误原因
    "exceptionHandle" => \Fend\ExceptionHandle\FendExceptionHandle::class, //异常处理默认类，有需要可以替换成自己的逻辑、需要继承interface

];

```

## 异常处理类定制建议
用户自定义时可操作范围：
 * 控制返回用户输出的内容
 * 更改返回内容header
 * 更改HttpCode
 * 可选是否记录异常日志
 * 异常日志是否发警报(fend-plugin-alarm组件)
 * 此处抛出异常会导致白屏
 * 通过Di::factory()->getRequest();获取请求参数

```php
<?php
namespace Fend\ExceptionHandle;

use Fend\Di;
use Fend\Log\EagleEye;
use Fend\Logger;

class FendExceptionHandle implements ExceptionHandleInterface //所有自定义异常类必须实现此interface
{

    /**
     * @param \Throwable $e 异常
     * @param string $result 返回值
     *
     * @return string result结果
     * @throws \Throwable
     */
    public static function handle(\Throwable $e, string $result): string //handle 内为处理方法
    {
        $request = Di::factory()->getRequest();
        $response = Di::factory()->getResponse();

        $response->status(500);

        //record exception log
        Logger::exception(basename($e->getFile()), $e->getFile(), $e->getLine(), $e->getMessage(), $e->getCode(), $e->getTraceAsString(), array(
            "action"    => $request->header("HOST") . $request->server("REQUEST_URI"),
            "server_ip" => gethostname(),
        ));

        EagleEye::isEnable() && EagleEye::setRequestLogInfo("code", 500);
        EagleEye::isEnable() && EagleEye::setRequestLogInfo("backtrace", $e->getMessage() . "\r\n" . $e->getTraceAsString());
        return $result;
    }
}
```