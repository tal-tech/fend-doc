# 框架目录结构
一个良好的项目，需要一个清晰的目录结构

| 目录名称 | 定义 | 用途 |
| --- | --- | --- |
| app       | app           | 用户业务代码文件夹，所有对外服务接口封装 |
| - Config  | config         | 配置：Swoole服务容器配置，常量定义，Mysql配置，Redis配置等 |
| - Const   | 业务全局常量  | 保存着业务所需的所有常量，如启用fend-plugin-errorcode可以对异常错误码进行统一管理 |
| - Cache   | 缓存临时文件夹  | 保存项目中一些缓存：Smarty临时文件，FastRouter Cacahe文件 |
| - Exec    | 控制台脚本     | Crontab，建议考虑使用Symfony Console封装|
| - Http    | Controller    | HTTP对外服务业务，建议不同域名代码放在不同子文件夹下 |
| - Services| 服务层         | Service项目内标准服务封装，如果要对外建议controller封装 |
| - Model   | model层        | 数据模块封装目录，内含所有数据源调用封装 |
| - View    | view层，模板    | Smarty模板，如需请composer require fend-plug-tempalte |
| bin       | 命令行工具     | Swoole服务容器进程管理、辅助开发工具、Debug工具等 |
| coverage  | 单元测试结果    | 单元测试结果 内有report\index.html 可以查看代码覆盖率 |
| fend      | Framework 框架 | Fend框架，composer版本没有此目录 |
| logs      | 日志目录       | 日志目录，业务自定义日志，分级日志及链路跟踪日志 |
| test      | phpunit       | 框架及单元测试用例 |
| vendor    | Composer vendor | 所有composer组件都在这里 |
| www       | FPM模式请求入口 | index.php |
| init.php  | 框架初始化     | 命令行启动时可以引用此文件加载框架 |
| composer.json  | composer设置 | 所需组件及autoload设置所在地 |

注意：PSR4要求框架目录文件名及大小写必须和namespace必须完全相同 

注意：app内Http内文件首字母必须大写其他部分小写 

最近更新：

* 2019-11-11 跟随标准、更新框架根目录下目录大小写
    * App -\> app
    * Coverage -\> coverage
    * Fend -\> fend
    * Logs -\> logs
    * Test -\> test
    * Bin -\> bin