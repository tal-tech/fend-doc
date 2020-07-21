# 多进程脚本封装

功能介绍：适用于一些需要多进程并行处理的业务，在生产进程、消费进程之间通过channel传递任务，如数据抓取，迁移，推送

整体实现结构

![multiprocess](../assets/multiprocess_class.png)

类实现在/fend/process/manage.php 内

使用方法为继承此类

例子：

```php
require_once(dirname(dirname(__DIR__)) . "/router.php");

if (!extension_loaded('swoole')) {
    die('swoole extension was not found' . PHP_EOL);
}
if (PHP_SAPI != 'cli') {
    die("this service is only run in cli mode..." . PHP_EOL);
}

class AsyncTask extends Fend_Process_manage
{

    public function start()
    {
        echo 'asynctask service started...' . PHP_EOL;
        Fend_Log::write("service started pid=" . getmypid(), $this->config['logName']);
        $this->monitorStart();
    }

    public function hook()
    {
        $redisObj = Fend_Cache_Redis::Factory("cacheRedis");

        Fend_Log::write("worker started pid=" . getmypid(), $this->config['logName']);

        while (true) {
            //刷新进程时间，如果长时间不执行这个函数，会被kill
            $this->flushTimestamp(getmypid());

            //如果没有任务那么阻塞1秒等待
            $taskString = $redisObj->brpop("async_task_queue", 1);
            if (!$taskString) {
                sleep(1);
                continue;
            }
            $taskString = $taskString[1];
            Fend_Di::factory()->set("task", $taskString);

            $task = json_decode($taskString, true);
            //check task
            if (!isset($task["method"]) || empty($task["method"]) || !isset($task["class"]) || empty($task["class"])) {
                Fend_Log::write("task format wrong:" . $taskString, $this->config['logName']);
                continue;
            }
            $methods = get_class_methods($task["class"]);
            if (!in_array($task["method"], $methods)) {
                Fend_Log::write("class method not found:" . $taskString, $this->config['logName']);
                continue;
            }

            //do the work
            // if timeout will be killed
            try {
                $obj = new $task["class"]();
                call_user_func(array(
                    $obj,
                    $task["method"]
                ), ...$task["param"]);
                //$obj->$task["method"]($task["param"]);
            } catch (Fend_RetryException $e) {
                //此异常用于业务逻辑判断业务是否异常
                //如果异常抛出后这里接收到会将retry字段进行-1操作
                //如果仍旧有剩余数值那么会放回任务队列
                //如果没有剩余数值，那么会记录任务日志，放弃继续执行
                if (!isset($task["retry"]) || $task["retry"] - 1 <= 0) {
                    Fend_Log::write("retry is zero:" . $taskString . "|||" . $e->getMessage() . "(" . $e->getCode() . ")", $this->config['logName']);
                    continue;
                }
                //desc retry count
                $task["retry"]--;
                $taskString = json_encode($task);
                Fend_Log::write("retry task:" . $taskString, $this->config['logName']);
                $redisObj->lpush("async_task_queue", $taskString);
            } catch (Throwable $e) {
                Fend_Log::write("exception:" . $taskString . "|||" . $e->getMessage() . "(" . $e->getCode() . ")", $this->config['logName']);
            }
        }

    }

    public function exiting()
    {
        //这里是处理任务被杀的时候会被执行的
        //目前不是kill 9，可能会有kill不掉的情况
        $task = Fend_Di::factory()->get("task");
        Fend_Log::write("killed timeout:" . $task, $this->config['logName']);

    }
}


$config = [
    //进程名称前缀
    'pre'        => 'xes_',

    //进程名称
    'name'       => 'asynctask',

    //服务日志输出路径
    'logName'    => 'asynctask.log',

    //进程数
    'processNum' => '20',

    //心跳，如果一个进程执行超过10秒就会被重启
    'heartTime'  => 10,
];
$app    = new AsyncTask($config);
$app->start();
```



