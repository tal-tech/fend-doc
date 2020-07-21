### Symfony Console组件

命令行脚本会随着时间增长越来越多，散乱，不好管理，并且每个都需要实现参数检测、帮助、整理 

Symfony Console能够较好的管理这些命令脚本，并且美化输出自动生成帮助等功能。 

### 引入组件
```bash
composer require symfony/console 4.3
```

### 添加新命令方法

 * 创建命令定义文件放置在Exec\Command内，类必须继承baseCommand
 * 类内定义 名称signature 和 desc 用途
 * 命令格式需要定义 params 变量 一个配置一行，配置格式为 配置键名|是否可选|参数说明 具体可以看swoole command
 * 针对命令的调用都会请求到handle内自行处理

### 例子展示

 * 例如：Exec\Command\Swoole 演示 
 
```php
   Swoole的param定义为：
       "configFile|required|配置文件",//配置文件路径（必填）
       "operation|optional|swoole services", //操作（可选）
       "mode|optional|debug?",//（可选选项）
       "pidFile|optional|pid file" //指定pid文件路径(可选)
   
   命令执行样例：
   php bin/fend list //列出所有可用模块
   php bin/fend swoole -h //查看模块参数帮助
   php bin/fend swoole -c app/Config/Swoole.php start //开启服务
   php bin/fend swoole  -c app/Config/Swoole.php -d 1 start //开启服务 debug开发模式
   
```

Symfony console 会调用Handle函数
   
