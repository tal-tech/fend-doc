# Swoole服务容器组件

Fend Swoole服务目前已经做了良好的封装，用户可以很容易从FPM切换到Swoole模式

特点：
 * 空跑QPS 10倍提升
 * 常驻内存提供服务，减少解释PHP时间
 * 支持本地cache
 * 同步调用、异步调用、以及未来支持的协程
 * 多协议：HTTP、Websocket、TCP、UDP
 * 服务端及客户端 长链接 到数据层及业务接口

另外有一些注意事项需要注意，相关信息放到了[Swoole开发人员使用注意事项](/component/swooleserver/swooledev.md)


## 服务组成
 
**bin/fend 或 bin/start.php** 服务重启、reload、kill控制工具

**app/Config/Swoole.php**，根据配置启动监听对应接口协议

**fend/Server/Baseserver.php** 根据配置注册监听指定端口及协议，并将事件捆绑到/Fend/Dispatcher/对应协议.php内

**fend/Server/Dispatcher/\*.php** 绑定Swoole事件，并在事件内根据业务触发调用不同的Controller，所有业务都是以MVC方式组装的只是不同协议触发不同Controller，这样可以方便适配多种协议

