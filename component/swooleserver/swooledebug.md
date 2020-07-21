# 错误查看及故障排查技巧

### Worker占用CPU 100%，服务不响应

strace -p worker进程pid

查看输出内容是否重复有规律，一般是代码死循环导致此类问题

---

### 接口返回错误排查

#### 方法1

在请求接口参数增加wxdebug=1即可看到一些错误信息及详细调用堆栈如：

[http://xxxxx.com/user/login?wxdebug=1](http://xxxxx.com/user/login?wxdebug=1)

#### 方法2

查看app/Config/Swoole.php内配置的log\_file，一般swoole服务异常都会在这里有日志输出

#### 方法3

线下开发时可开启PHP错误日志，在php.ini 内

```
error_log = /tmp/php_error.log
log_errors = On
```

重启服务即可生效，开发过程中使用tail -f /tmp/php\_error.log 可以实时看错误信息

#### 方法4

开发环境时可以在服务启动之前关闭/conf/swoole.php内daemonize为0
另外启动命令行时增加-d 1参数

```
daemonize = 0
```

启动服务后，常见故障信息都会在控制台直接输出，命令行会阻塞执行，退出ctrl+c

---

### GDB在线debug

具体技巧请详细阅读：[https://wiki.swoole.com/wiki/page/442.html](https://wiki.swoole.com/wiki/page/442.html)

