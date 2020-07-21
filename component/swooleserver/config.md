# Swoole服务配置

默认配置/conf/swoole.php

```php
return array(
    //主服务，选主服务 建议按 websocket（http） > http > udp || tcp 顺序创建 ,websocket只能作为主进程
    "server" => array(
        "server_name" => "fend",
        "host"        => "0.0.0.0",
        "port"        => 80,
        "class"       => "swoole_http_server",//可选项：swoole_websocket_server/swoole_http_server/swoole_server
        "socket"      => SWOOLE_SOCK_TCP,
        'classname'   => '\Fend\Dispatcher\Http',
        "loglevel"    => \Fend\Log::LOG_TYPE_INFO,
        "logpath"     => 'logs/eagleeye/',
    ),

    "listen" => array(
        /*
        //tcp 服务端口
        "httpserver" => array(
            "host"       => "0.0.0.0",
            "port"       => 9572,
            "socket"     => SWOOLE_SOCK_TCP,
            'classname'  => '\Fend\Dispatcher\Http',
            "protocol"   => array(
                'open_http_protocol' => 1,
                'open_tcp_nodelay'   => 1,
            ),
        )
        */
    ),

    //共享swoole table 具体请查看 swoole table
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

    "swoole" => array(
        //'user' => 'www',
        //'group' => 'www',
        'dispatch_mode'      => 1,
        'package_max_length' => 2097152, // 1024 * 1024 * 2,
        'buffer_output_size' => 3145728, //1024 * 1024 * 3,
        'pipe_buffer_size'   => 33554432, //1024 * 1024 * 32,

        'backlog'                   => 30000,  //listen连接队列长度
        'open_tcp_nodelay'          => 1,//如果数据包很小发送会延迟，开启这个可以提高响应速度
        'heartbeat_idle_time'       => 180, //tcp心跳检测空闲时间超时时间
        'heartbeat_check_interval'  => 60,//周期心跳检测间隔

        'open_cpu_affinity' => 1, //打开cpu亲和,会提高性能
        'worker_num'        => 1, //处理业务worker个数，生产环境建议开大，一般要经过压测决定环境worker个数
        //'task_worker_num'   => 1,
        'max_request'       => 1, //worker处理多少个请求后会重启，生产环境建议开到1w以上
        //'task_max_request'  => 1,
        'discard_timeout_request' => false, //连接断开后，不停止处理当前请求（否则会导致一些数据提交一半）
        'log_level'         => 2, //swoole 日志级别 Info
        'log_file'          => '/tmp/baseserver.log',//swoole 系统日志，任何代码内echo都会在这里输出
        'task_tmpdir'       => '/tmp/baseserver/',//task 投递内容过长时，会临时保存在这里，请将tmp设置使用内存
        'pid_file'          => '/tmp/baseserver.pid',//进程pid保存文件路径，请写绝对路径，保存的是master进程pid

        'request_slowlog_timeout' => 3,
        'request_slowlog_file'    => '/tmp/base_slow.log', //worker请求慢日志记录，如果一个请求处理业务超过指定时间3秒那么将会记录此日志
        'trace_event_worker'      => true,

        'daemonize' => 1,//注意测试使用配置0，线上建议1
    ),
);

```



