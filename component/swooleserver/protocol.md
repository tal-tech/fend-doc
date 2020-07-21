# 协议及适配

目前框架主要支持http协议，其他协议如果需要可以随时增加

Swoole服务启动后会根据app/Config/Swoole.php配置对指定端口及协议进行配置。

其中dispatcher配置决定了对应端口收到消息后如何处理

如/Fend/Dispatcher/Http.php 内如果收到Http请求时，会触发onRequest函数，并传入request、response对象

用户可以根据需要在这里对不同事件进行定制

正在筹划可注册事件方式
