# Swoole开发注意事项

---

### 业务代码更新问题

服务启动后已经加载的php代码是不会随着磁盘内php更新而更新，需要重启服务才会刷新，如果在本地开发建议使用命令行-d 1开启debug模式，具体看服务启动及管理

---

### exit\(\)、die\(\)

业务逻辑代码中禁止使用exit、die，如果有需要终止执行后续代码，可以通过抛出异常中断，新版本Swoole会拦截exit，抛出\Swoole\ExitException但是不能透传任何信息

```
throw new \Fend\Exception\ExitException("退出原因",数值错误码);//即可终止当前请求
```

---

### response-&gt;end\(\)

业务代码中不要执行$response-&gt;end\(\)\(http协议返回结果\)，建议统一让/fend/dispatcher/http.php内进行统一返回，因为end执行后后续输出将不会推送到客户端。

---

### require\_once\|include\_once

在一些模板使用的习惯中常见一些研发人员会使用include\_once加载一个带php代码模板

在fpm下一切正常

在swoole下由于worker处理完一次请求后不会释放任何资源，once只会执行一次，会造成模板不再显示

---

### global\|static

业务代码尽量不要使用任何global及static对处理数据进行暂存，否则会出现数据串台情况

---

### sleep

不推荐使用，会阻塞进程导致一些注册时间触发延后

---

### while、for循环

务必保证业务代码中不存在死循环现象，之前碰到过因为某业务逻辑缺陷一直while循环，导致cpu过高，请求没有响应。

如果发现一些worker cpu使用率偏高，可以使用strace -p 进程pid 监听看输出是否有规律。

如果有规律，可以使用gdb -p 进程pid 并引入source php-src/.gdbinit通过zbacktrace查询当前调用堆栈来判断是否死循环

worker是永远不会超时的，后续fend会增加worker心跳检测此类情况

目前swoole也提供了类似功能 请参考request\_slowlog\_file 配置选项

---

### stat、fstat、filemtime

为了提高性能，php对这几个函数加了cache，请具体参考：[http://php.net/manual/zh/function.clearstatcache.php](http://php.net/manual/zh/function.clearstatcache.php)

---

### 随机数

mt\_rand、array\_rand、shuffle都会受到影响，建议每个worker启动时执行mt\_srand

