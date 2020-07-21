# 协程版本更新历史
# fend-core
## 1.3.39
 * 日志路径中出现两个/问题修复  -姚超峰
 * Request-\>get\post 如指定filter时 100.00被过滤成100问题 -姚超峰

## 1.3.38
 * 修复RPCID计数器问题 -韩冬辉、长龙

## 1.3.37
 * $Request-\>request default value to null -绍民、长龙

## 1.3.36
 * FendHttp::httpRequest 增加retry -王坤 长龙
 * FendHttp::httpRequest 增加connect\_timeout extra选项 自定义connect时间 -李长林 长龙
 * wxdebug 模式 json decode 设置为中文不编码模式，附带原文 -杨坤

## 1.3.35
 * 修复Funcs\FendHttp::getMulti post必须设置证书限制 -郭恩玮

## 1.3.34
 * response-\>csvHead csvAppendWrite 用于csv输出 -姚超峰,长龙
 
## 1.3.33
 * response-\>status设置后没有改变trace日志记录 -欧阳源
 * debug SQL记录增加读写对象类型 -长龙
 
## 1.3.32
 * 修复: debug 在接口没有返回时不输出console问题 -长龙

## 1.3.31
 * 修复：debug getTraceString 个别情况下没有class会显示warning -李宗源 丹阳 
 * 新功能：Request 增加getMethod 用于获取当前请求是POST、GET -李宗源 长龙
 * 优化：Debug explain 对SQL 解释失败warning问题 -长龙
 
## 1.3.28
 * 新特性：Request对象增加setController、getController用于在Controller Init触发时获取调用controller信息 -姚超峰 长龙

## 1.3.27
 * 改进：getIp时如果在命令行下不报错 -王坤 长龙
 * 改进：Request::Get Post Request 默认值设置为空字符串而非null -李宗源 长龙
 * 修复：Request::getParam default非默认值时get无法获取问题 -李宗源 长龙

## 1.3.26
 * Debug 界面postman下无法工作，去掉JS使用 -郭海龙，长龙

## 1.3.25
 * Request 设置post数据时，被转大写修正 -王坤，长龙

## 1.3.24
 * tal\_trace\_id及tal\_rpc\_id统一更换成traceid及rpcid header，与网关联通 -王坤、长龙、张报

## 1.3.23
 * Request对象：get post request cookie file 增加key大小写不敏感获取参数 -韩冬辉，长龙

## 1.3.22
 * 修复 Exception Handle Exception参数改为Throwable -王坤、长龙

## 1.3.21
 * 增加Exception Handle Interface 及异常处理可配置替换机制 -周聪 长龙

## 1.3.17
 * FendHttp HttpRequest 证书变量错误修复  -长龙

## 1.3.16
 * 修复swoole协程模式下swoole request返回post为null导致$Request\-\>request()返回null问题 -韩冬辉 长龙

## 1.3.15
  * 修复getIP函数准确性 -王坤、长龙、张报
 
## 1.3.10
 * fend日志经过与基础架构组日志中心协商，统一了日志清理及收集存储标准，请用户注意app\Config\Fend.php内logPrefix设置内容及结尾不要加\_ -王坤、长龙、王东、吕昌龙、韩文杰

## 1.3.9
 * Request 增加ios、android、微信内浏览器、微信小程序调用检测 - 夏磊
 * trace 长度未设置报错问题修复 -杜超群

## 1.3.8
 * 修复gethttp返回结果不记录问题 -长龙
 * trace增加选项，增加 返回结果超过过长会截断、不记录、一直记录选项 -王坤、郭绍民

## 1.3.7
 * request增加isAjax检测 -长龙
 * Debug模式增加swoole协程标志swoole\_v1为非协程版 swoole\_v4为协程版
 * Log记录日志路径只有框架目录开始的相对路径 -王坤
 
## 1.3.6
 * request 增加参数过滤功能 -杜超群

## 1.3.5
 * request\-\>request 没有获取到提示warning修复 -秘静雅

## 1.3.0
 * 支持协程
   * 增加RequestContext封装、Context封装、Coroutine封装、支持变量xxx.xxx.xx 多维设置获取、兼容三个工作模式 -丹阳
   * 增加Pool连接池管理类，兼容阻塞Swoole、协程Swoole -丹阳
   * 增加Channel封装，兼容FPM、阻塞Swoole、协程Swoole  -丹阳
   * DI支持注入生产类，支持变量xxx.xxx.xx 多维设置获取。并提供make功能 -丹阳
   * Di Request、Response 改为请求级别共享 -长龙
   * Fend 基础类不再支持magic 变量设置 -丹阳、刘帅
   * Debug兼容模式，协程支持改造 -长龙
   * Trace日志改造 -长龙
   
## 1.2.51
 * 删除Fend::\_\_get返回为引用问题 -长龙、邵王镇

## 1.2.50
 * 日志路径中出现两个/问题修复  -姚超峰
 * Request-\>get\post 如指定filter时 100.00被过滤成100问题 -姚超峰

## 1.2.49
 * $Request-\>request default value to null -绍民、长龙
 * FendHttp::httpRequest|getUrl|getHttp 记录trace增加curl\_info -王坤、长龙

## 1.2.48
 * FendHttp::httpRequest 增加retry -王坤 长龙
 * FendHttp::httpRequest 增加connect\_timeout extra选项 自定义connect时间 -李长林 长龙
 * wxdebug 模式 json decode 设置为中文不编码模式，附带原文 -杨坤

## 1.2.47
 * 修复Funcs\FendHttp::getMulti post必须设置证书限制 -郭恩玮
 
## 1.2.46
 * response-\>csvHead csvAppendWrite 用于csv输出 -姚超峰,长龙

## 1.2.45
 * response-\>status设置后没有改变trace日志记录 -欧阳源
 * debug 界面显示 instance name -长龙

## 1.2.44
 * 修复：中间件依赖DI的对象被动注入功能，1.2版本没有提供，此次将此特性更新到1.2版本内 -赵禹、丹阳、长龙

## 1.2.43
 * 修复: debug 在接口没有返回时不输出console问题 -长龙

## 1.2.42
 * 修复：debug getTraceString 个别情况下没有class会显示warning -李宗源 丹阳 
 * 新功能：Request 增加getMethod 用于获取当前请求是POST、GET -李宗源 长龙
 * 优化：Debug explain 对SQL 解释失败warning问题 -长龙
 
## 1.2.39
 * 新特性：Request对象增加setController、getController用于在Controller Init触发时获取调用controller信息 -姚超峰 长龙

## 1.2.38
 * 改进：getIp时如果在命令行下不报错 -王坤 长龙
 * 改进：Request::Get Post Request 默认值设置为空字符串而非null -李宗源 长龙
 * 修复：Request::getParam default非默认值时get无法获取问题 -李宗源 长龙

## 1.2.37
 * Debug 界面postman下无法工作，去掉JS使用 -郭海龙，长龙

## 1.2.36
 * Request 设置post数据时，被转大写修正 -王坤，长龙

## 1.2.35
 * tal\_trace\_id及tal\_rpc\_id统一更换成traceid及rpcid header，与网关联通 -王坤、长龙、张报

## 1.2.34
 * Request对象：get post request cookie file 增加key大小写不敏感获取参数 -韩冬辉，长龙

## 1.2.33
 * 修复 Exception Handle Exception参数改为Throwable -王坤、长龙

## 1.2.32
 * 增加Exception Handle Interface 及异常处理可配置替换机制 -周聪 长龙

## 1.2.29
 * FendHttp HttpRequest 证书变量错误修复  -长龙

## 1.2.28
 * Config::get时支持传递默认参数，如果配置文件或对应选项不存在返回默认值 -长龙
 * FendHttp增加httpRequset方法，get请求会自动将data拼到网址，请求错误会抛异常，header支持kv及string[]方式传递，data支持字符串或kv数组，支持自定义curlopt，支持获取返回header及getinfo -张云飞 李宗
源 长龙
 * FendHttp getUrl及getHttp函数优化data支持kv及string参数,header支持kv数组及string[]数组 -张云飞 长龙
 * Logger Log类对于输入数据key检测弱类型检测修复 -长龙

## 1.2.27
 * 修复swoole协程模式下swoole request返回post为null导致$Request\-\>request()返回null问题 -韩冬辉 长龙

## 1.2.26
 * 修复getIP函数准确性 -王坤、长龙、张报

## 1.2.24
 * fend日志经过与基础架构组日志中心协商，统一了日志清理及收集存储标准，请用户注意app\Config\Fend.php内logPrefix设置内容及结尾不要加\_ -王坤、长龙、王东、吕昌龙、韩文杰
 
## 1.2.23
 * Request 增加ios、android、微信内浏览器、微信小程序调用检测 - 夏磊
 * trace 长度未设置报错问题修复 -杜超群

## 1.2.22
 * 修复gethttp返回结果不记录问题 -长龙
 * trace增加选项，增加 返回结果超过过长会截断、不记录、一直记录选项 -王坤、郭绍民

## 1.2.21 
 * 修复funcs 内fendArray 冗余代码 - 韩冬辉
 * Request 增加isAjax检测 -长龙
 * Log记录日志路径只有框架目录开始的相对路径 -王坤

## 1.2.20
 * 修复错误引用 -长龙

## 1.2.19
 * request 增加参数过滤功能 -杜超群

## 1.2.18
 * request\-\>request 没有获取到提示warning修复 -秘静雅

## 1.2.17
 * swoole模式下小概率日志写错问题 -长龙

## 1.2.16
 * 日志落地增加文件名前缀，用于区分不同项目日志  -王坤

## 1.2.15
 * 补充Request $\_REQUEST 方法 -张云飞

## 1.2.14
 * Config 增加点分支持，现在可以通过类似`Redis.default.host`的格式去获取配置中的子项
 * 补全了Config的单元测试

## 1.2.13
 * 网址内参数加wxdebug=1可以查看debug模式，本地性能分析支持：
    * 0：关闭
    * 1：开启wxdebug模式 html界面
    * 2：开启wxdebug模式 var\_dump输出所有信息
    * 3：不开启debug界面 记录xhprof Profile
 
## 1.2.12
 * 遵循运维对日志的规范，Fend落日志不再使用子文件夹进行区分，统一放到一个目录进行管理 -孙海晓 韩文杰

## 1.2.11
 * FendHttp获取参数时，value为0时被过滤成null问题 -韩孝
 
## 1.2.8
 * Log输出日志文件名规则、日志格式可配置\App\Config\Fend.php -木荣、秘静雅、周聪，张云飞，超群\徐长龙
 * Trace 日志耗时字段过长问题修复 -黄凯文


-----

# fend-plugin-alarm
## 1.3.0

* 创建协程版本
* 将Alarm对象纳入DI管理

## 1.2.4

* 补全push方法参数，现在push方法参数跟底层SDK的参数完全一致了。
* 补全了单元测试，现在单测覆盖率达到100%。
-----

# fend-plugin-cache
## 1.3.0 版本
阻塞Swoole + FPM + Swoole协程版
 
## 1.2.8
 * 更新memcache配置 -长龙

-----

# fend-plugin-console
## 1.2.9

* 移除了部分无用代码
* 完善了单元测试，目前单测覆盖率达100%
-----

# fend-plugin-db
## 1.3.16
 * DBModel\DBNCModel 读写链接用时链接 -郭绍民、绍王镇、长龙、周伟伟

## 1.3.15
 * PDO Escape 过滤错误修复 -长龙 
 * SQL 安全检查误判 record(为 ord() 修复 -黄伟杰、长龙

## 1.3.14
 * Read\Write:增加getLeftJoinGroupHavingListByWhere 支持 -李兴部 徐长龙
 * Read\Write:增加group having支持 -徐长龙

## 1.3.13
 * Read\Write:SQLBuilder 增加互斥锁、共享锁 -长龙

## 1.3.12
 * 修复getLeftJoinCountByWhere错误引用 -长龙

## 1.3.11
 * count(1) => count(\*) -杨坤
 * DBNCModel\DBModel Factory comment return static not DBNCModel -李博
 * DBNCModel\DBModel add getLeftJoinCountByWhere getLeftJoinListByWhere -张保磊 长龙

## 1.3.10
 * 数据更新update set xxx = xxx + 1类操作，增加支持prepare -杨坤 长龙 

## 1.3.9
 * 协程模式下不允许跨协程调用同一个实例 model -长龙
 * Model对应的链接在事务中，自动使用写库查询数据 -绍王镇 长龙

## 1.3.8
 * Read|Write 增加left join函数 -张保磊

## 1.3.7
 * 改进 批量添加 增加数据过滤 -赵剑、长龙

## 1.3.6
 * 为适应业务需要DBNCModel|DBModel getModel从protected转为public -周聪、长龙

## 1.3.5
 * SQLBuilder增加 count = count + 1生成 -王坤
 * SQLBuilder检测数据中安全检测有双引号就会拒绝执行，现在关闭 -王坤 长龙

## 1.3.4
 * DBModel Cache key 重名导致cache错误问题修复 -长龙

## 1.3.3
 * Read/Write执行前会清理之前SQL信息残留 -长龙
 * 增加getSumByGroup 用于group by count -邵王镇
 * 修复getAffectRow错误问题 -长龙
 * Model增加getSumByGroup -长龙
 * 修正Model测试用例最后一个参数没有同步修改问题 -长龙

## 1.3.2
 * SQL BUilder 数值过滤强转导致0开头字符串错误识别问题 -韩孝

## 1.3.1
 * SQL Builder 修复数值0与字符串相等成立问题 -韩孝

## 1.3.0
 * DBModel DBNCModel 支持协程 -丹阳
 * SQLBuilder 增加clean参数功能-长龙、丹阳
 
## 1.2.29
 * DBModel\DBNCModel 读写链接用时链接 -郭绍民、绍王镇、长龙、周伟伟

## 1.2.28
 * PDO Escape 过滤错误修复 -长龙 
 * SQL 安全检查误判 record(为 ord() 修复 -黄伟杰、长龙

## 1.2.27
 * Read\Write:增加getLeftJoinGroupHavingListByWhere 支持 -李兴部 徐长龙
 * Read\Write:增加group having支持 -徐长龙

## 1.2.26
 * Read\Write:SQLBuilder 增加互斥锁、共享锁 -长龙

## 1.2.25
 * 修复getLeftJoinCountByWhere错误引用 -长龙

## 1.2.24 
 * count(1) => count(\*) -杨坤
 * DBNCModel\DBModel Factory comment return static not DBNCModel -李博
 * DBNCModel\DBModel add getLeftJoinCountByWhere getLeftJoinListByWhere -张保磊 长龙

## 1.2.23
 * 数据更新update set xxx = xxx + 1类操作，增加支持prepare -杨坤 长龙 

## 1.2.22
 * Model对应的链接在事务中，自动使用写库查询数据 -绍王镇 长龙 

## 1.2.21
 * Read|Write 增加left join函数 -张保磊

## 1.2.20
 * 改进 批量添加 增加数据过滤 -赵剑、长龙

## 1.2.19
 * 为适应业务需要DBNCModel|DBModel getModel从protected转为public -周聪、长龙

## 1.2.18
 * SQLBuilder增加 count = count + 1生成 -王坤
 * SQLBuilder检测数据中安全检测有双引号就会拒绝执行，现在关闭 -王坤 长龙

## 1.2.17
 * DBModel Cache key 重名导致cache错误问题修复 -长龙

## 1.2.16
 * Model增加getSumByGroup -长龙
 * 修正Model测试用例最后一个参数没有同步修改问题 -长龙
 * Model Read Write 增加getSumByGroupList -长龙 邵王镇

## 1.2.15
 * Read/Write执行前会清理之前SQL信息残留 -长龙
 * 增加getSumByGroup 用于group by count -邵王镇

## 1.2.14
 * SQL BUilder 数值过滤强转导致0开头字符串错误识别问题 -韩孝

## 1.2.13
 * SQL Builder 修复数值0与字符串相等成立问题 -韩孝

## 1.2.12
 * SQL Buider where只支持大写AND OR，现在改为兼容大小写 -韩孝
 
## 1.2.11
 * DBNCModel、DBModel增加大量单元测试、DBNCModel增加delbyid -徐长龙
 
## 1.2.10
 * DBModel及DBNCModel factory修复只能创建父类问题 -秘静雅
 
## 1.2.9
 * DBModel及DBNCModel 增加getTableName，用于获取表名 -秘静雅

## 1.2.8
  * Log输出日志文件名规则、日志格式可配置\App\Config\Fend.php -木荣、秘静雅、周聪，张云飞，超群\徐长龙

-----

# fend-plugin-env
## 1.0.0
 * Env 环境变量始化
-----

# fend-plugin-fastrouter
## 1.3.1
* 修复fastrouter，域名不匹配下不使用default路由配置错误-郭海龙 长龙

## 1.2.10
* 修复fastrouter，域名不匹配下不使用default路由配置错误-郭海龙 长龙

## 1.2.9

* 增加一个配置项`open_cache`, 用于配置fastrouter是否使用缓存文件，默认为开启，在开发环境下可设置为false方便修改调试。

## 1.2.8

* 修复了一处抛出异常的错误
* 移除了不必要的代码
* 完善了单元测试，目前单测覆盖率达到100%

-----

# fend-plugin-kafka
## 1.3.8
 * V2 增加appid及appkey设置 在消费者上 -建智、超群、长龙
 * V2 配置增加每次消费消息个数 -建智、超群、长龙
 
## 1.3.5
 * 修复kafka v2 驱动 单个ack失败问题 -郭恩玮

## 1.3.4
 * 新增kafka v2 驱动 -建智 长龙

## 1.3.3
 * 更新，请求失败时，记录返回内容详细信息 -王坤，长龙

## 1.3.2
 * 更新，Queue工厂挪出到新组件fend-plugin-queue，kafka插件新版本会自动依赖fend-plugin-queue -长龙
 * 新增fend-plugin-rabbitmq组件 -周聪 长龙

## 1.3.1
 * 修复factory非单例问题 -王坤、长龙

## 1.2.15
 * V2 增加appid及appkey设置 在消费者上 -建智、超群、长龙
 * V2 配置增加每次消费消息个数 -建智、超群、长龙
 
## 1.2.13
 * 修复kafka v2 驱动 单个ack失败问题 -郭恩玮

## 1.2.12
 * 新增kafka v2 驱动 -建智 长龙

## 1.2.11
 * 更新，请求失败时，记录返回内容详细信息 -王坤，长龙

## 1.2.10
 * 更新，Queue工厂挪出到新组件fend-plugin-queue，kafka插件新版本会自动依赖fend-plugin-queue -长龙
 * 新增fend-plugin-rabbitmq组件 -周聪 长龙

## 1.2.9
 * 修复factory非单例问题 -王坤、长龙

## 1.2.8
 * 消费 demo增加自定义进程名称选项 -杜超群
 
## 1.2.7
 * 消费进程监控，如果消费进程长时间没有执行心跳函数，那么会被强制kill，用于预防进程卡死阻塞 -徐长龙
 * consumer及producer 改为懒加载方式，使用时才会初始化 -郭绍民
 * 增加输入参数校验 -郭绍民
 * 消费ack内部变量错误问题  -郭绍民

-----

# fend-plugin-memcache
## 1.2.8
 * memcache 配置改进，添加更多选项 -长龙

## 1.2.7
 * 增加pack_type配置项，1 代表json序列化，2代表php自带序列化
 * 完善单元测试，覆盖率达到100%
 

-----

# fend-plugin-mysqli
## 1.3.7
 * 为了更好应对网络抖动，增加失败重试次数2-》4,并延迟100ms后重连 -韩冬辉、长龙

## 1.3.6
 * Debug界面显示instance name sql -长龙

## 1.3.5
 * 修复：sql异常时debug界面看不到具体sql -张保磊

## 1.3.4
 * mysqli 重连时，检测链接是否可用覆盖掉之前错误信息问题修复 -宋明伟 长龙

## 1.3.2
 * 事务进行中，重连导致后续执行在非事务过程中，修复 -长龙

## 1.3.1
 * 底层不是单例问题 -王坤

## 1.3.0
 * 增加连接池支持 -丹阳
 * MysqlI驱动重连机制修复 -丹阳

## 1.2.15
 * 为了更好应对网络抖动，增加失败重试次数2-》4,并延迟100ms后重连 -韩冬辉、长龙

## 1.2.14
 * debug界面增加instance name -长龙

## 1.2.13
 * 修复：sql异常时debug界面看不到具体sql -张保磊

## 1.2.12
 * mysqli 重连时，检测链接是否可用覆盖掉之前错误信息问题修复 -宋明伟 长龙

## 1.2.11
 * 事务进行中，重连导致后续执行在非事务过程中，修复 -长龙
 * mysqli 驱动改动为，错误报异常 -杜超群 长龙


## 1.2.10
 * 单例被close后重连失败问题 -长龙

## 1.2.9
 * 底层不是单例问题 -王坤

## 1.2.8
 * composer.json增加ext-mysqlnd 依赖检测 -王坤

-----

# fend-plugin-pdo
## 1.3.9
* 连接时间没有记录问题修复 -长龙、周伟伟
 * 删除冗余日志代码 -长龙、周伟伟

## 1.3.8
 * 增加失败重试次数,并且延迟100ms后重连 -韩冬辉、长龙

## 1.3.7
 * 修复esacpe 截断json结尾问题 -郭绍民、长龙

## 1.3.6
 * sql异常时debug界面看不到具体sql -张保磊
 * 增加instance name 在debug界面 -长龙

## 1.3.5
 * 非事务过程中rollback不抛出异常 -王坤 长龙

## 1.3.4
 * 错误namespace导致pdo无法初始化 -韩冬辉 长龙

## 1.3.3
 * 事务进行中，重连导致后续执行在非事务过程中，修复 -长龙
 * mysql gone away 新测试方式，测试出异常不仅仅PDOException -超群 长龙

## 1.3.2
 * 底层不是单例问题修复 -王坤

## 1.3.1
 * 底层不是单例问题修复 -王坤

## 1.3.0
 * 增加协程连接池支持 - 丹阳

## 1.2.16
 * 连接时间没有记录问题修复 -长龙、周伟伟
 * 删除冗余日志代码 -长龙、周伟伟

## 1.2.15
 * 增加失败重试次数,并且延迟100ms后重连 -韩冬辉、长龙

## 1.2.14
 * 修复esacpe 错误截断json结尾问题 -郭绍民、长龙
 
## 1.2.13
 * sql异常时debug界面看不到具体sql -张保磊
 * 增加instance name 在debug界面 -长龙

## 1.2.12
 * 非事务过程中rollback不抛出异常 -王坤 长龙

## 1.2.11
 * 事务过程中重连导致后续执行在非事务过程中，修复 -长龙
 * mysql gone away 新测试方式，测试出异常不仅仅PDOException -超群 长龙

## 1.2.10
 * 底层不是单例问题修复 -王坤

## 1.2.9
 * PDO链接错误时，错误的处理错误信息 -王坤

## 1.2.8
 * composer.json增加ext-mysqlnd 依赖检测 -王坤

-----

# fend-plugin-queue
## 1.3.3
 * 增加 RabbmitMQ SideCar V2 -长龙

## 1.3.2
 * queue 增加 kafka v2版本 -建智 长龙

## 1.3.1
 * queue 拆分 -长龙

## 1.2.3
 * 增加 RabbmitMQ SideCar V2 -长龙

## 1.2.2
 * queue 增加 kafka v2版本 -建智 长龙

## 1.2.1
 * queue 拆分 -长龙

-----

# fend-plugin-rabbitmq
## 1.3.1
 * 新版sidecar v2 rabbitmq驱动 -长龙

## 1.3.0
 * 提供rabbitmq驱动 -长龙

## 1.2.1
 * 新版sidecar v2 rabbitmq驱动 -长龙

## 1.2.0
 * 提供rabbitmq驱动 -长龙

-----

# fend-plugin-redis
## 1.3.1
 * 增加重连多次尝试及延迟200ms -周伟伟

## 1.3.0
* 增加协程支持
* 增加协程连接池

## 1.2.9
 * 增加重连多次尝试及延迟200ms -周伟伟

## 1.2.8
* 修复了Redis重连检测的一处BUG
* 完善了单元测试，目前覆盖率达到98%


-----

# fend-plugin-redismodel
## 1.3.8
* 增加sAddArray -李兴部
* 增加hdel、sadd及sremove支持批量 -李博

## 1.3.5
* getOrSet 生效时返回null修正 -李博 长龙

## 1.3.4
* set增加sRandMember操作，可以随机返回指定个数value -李寻愁 长龙

## 1.3.3
* 由于golang特殊要求，json\_decode 增加可配置转换成obj或数组 -邵王镇 长龙

## 1.3.2
* 修复 传入key参数必须按顺序问题 -刘纯 长龙

## 1.3.1
* 修复set方法，支持第三个参数传入数组[具体使用方法见Redis官方文档]
* 修复setnx方法，不再采用两段式调用，改用set方法代替

## 1.3.0
* 开启协程支持

## 1.2.12
* 新用法: zrem增加批量删除 -郭恩玮
* 新用法：param为空的时候不报notice -周聪、长龙

## 1.2.11 
* 增加sAddArray -李兴部

## 1.2.10
* 增加hdel、sadd及sremove支持批量 -李博

## 1.2.9
* getOrSet 生效时返回null修正 -李博 长龙

## 1.2.8
* set增加sRandMember操作，可以随机返回指定个数value -李博 长龙

## 1.2.7
* 由于golang特殊要求，json\_decode 增加可配置转换成obj或数组 -邵王镇 长龙

## 1.2.6
* 修复 传入key参数必须按顺序问题 -刘纯 长龙

## 1.2.5
* 修复set方法，支持第三个参数传入数组[具体使用方法见Redis官方文档]
* 修复setnx方法，不再采用两段式调用，改用set方法代替

## 1.2.4
* 对hash map类型相关的函数增加 ignoreFieldCheck 参数，默认为false。当该参数为true时，底层不再校验sub field是否存在于配置之中。（李丹阳）

## 1.2.3
* 修改了单元测试的命名空间，用于统一测试结果
* 完善了单元测试覆盖，现在除了publish和subscribe均已完成单元测试。

-----

# fend-plugin-router
## 1.3.1
* 用户能够通过Request->getController 获取到路由调用目标Controller 类名 及Action 名称 -姚超峰 长龙

## 1.3.0
* 协程版本支持

## 1.2.10
* 用户能够通过Request->getController 获取到路由调用目标Controller 类名 及Action 名称 -姚超峰 长龙

## 1.2.9
* dispatcher构造时如果没有传递配置那么使用默认router 丹阳

## 1.2.8

* 修复了一处抛出异常的错误 丹阳
* 移除了不必要的代码 丹阳
* 完善了单元测试，目前单测覆盖率达到100% 丹阳

-----

# fend-plugin-rpcserver
## 1.3.1
* 日志落地增加文件名前缀，用于区分不同项目日志  -王坤

## 1.3.0
* 开启Swoole协程支持，可以通过配置开启协程功能

## 1.2.10
 * 日志落地增加文件名前缀，用于区分不同项目日志  -王坤

## 1.2.9
 * 修复Table的缓存文件在第一次初始化时会报错的bug

## 1.2.8
 * Log输出日志文件名规则、日志格式可配置\App\Config\Fend.php -木荣、秘静雅、周
 聪，张云飞，超群\徐长龙

-----

# fend-plugin-server
## 1.3.5
 * trace中记录请求体body内容 -韩孝、长龙

## 1.3.4
 * tal\_trace\_id\tal\_rpc\_id 这两个header，现在改为traceid rpcid，与网关合并

## 1.3.3
 * 增加Exception Handle Interface 及异常处理可配置替换机制 -周聪 长龙

## 1.3.2
 * 修复reload不工作问题 -长龙
 * 修复swoole模式下wxdebug=2不工作问题 -长龙

## 1.3.1
 * 日志落地增加文件名前缀，用于区分不同项目日志  -王坤

## 1.3.0
 * 开启Swoole协程支持，可以通过配置开启协程功能
 
## 1.2.14
 * trace中记录请求体body内容 -韩孝、长龙

## 1.2.13
 * tal\_trace\_id\tal\_rpc\_id 这两个header，现在改为traceid rpcid，与网关合并

## 1.2.12
 * 增加Exception Handle Interface 及异常处理可配置替换机制 -周聪 长龙

## 1.2.11
 * 修复由于reload需求更改导致的，swoole下请求不到controller问题-长龙

## 1.2.10
 * 日志落地增加文件名前缀，用于区分不同项目日志  -王坤

## 1.2.9
 * 修复Table的缓存文件在第一次初始化时会报错的bug

## 1.2.8
 * Log输出日志文件名规则、日志格式可配置\App\Config\Fend.php -木荣、秘静雅、周
 聪，张云飞，超群\徐长龙

-----

# fend-plugin-template
## 1.3.2
 * PHP原生模板，增加模板变量isset检测 -望乐乐

## 1.3.1
 * PHP原生模板 -望乐乐

## 1.2.10
 * PHP原生模板，增加模板变量isset检测 -望乐乐

## 1.2.9
 * PHP原生模板 -望乐乐

## 1.2.8
 * Smarty 组件初始化

-----

# fend-plugin-validator
## 1.3.11
* RumValidator：添加emptystr规则  -刘木荣
* RumValidator：修复filter\_var使用问题 -刘木荣

## 1.3.10
 * 修复validation json检测及错误的单元测试  -张小旭，长龙

## 1.3.9
 * 增加类laravel validation检测 -刘木荣、秘静雅、杨云超、李宗源、孟祥存、段超、韩天峰、安国雷、韩刚

## 1.3.7
 * 增加array类型验证，目前只是检测是否为数组 -邵王镇

## 1.3.5
 * validateFilter修复，设置key但未设置val导致warning提示、修复默认值弱类型检测错误导致0无效问题，设置enum传入类型in_array类型不严谨问题 -秘静雅

## 1.3.4
 * validateFilter增加数组批量添加规则功能 -秘静雅 长龙

## 1.3.3
 * 修复validateFilter 非必填没有default参数，有warning提示问题 -任成龙

## 1.3.2
 * 修复validateFilter 正则表达式不生效问题 -任成龙

## 1.3.1
 * 配合中台架构规范化，validateFilter开启自定义错误码功能addRuleEx -邵王镇

## 1.2.19
* RumValidator：添加emptystr规则  -刘木荣
* RumValidator：修复filter\_var使用问题 -刘木荣

## 1.2.18
 * 修复validation json检测及错误的单元测试  -张小旭，长龙

## 1.2.17
 * 增加类laravel validation检测 -刘木荣、秘静雅、杨云超、李宗源、孟祥存、段超、韩天峰、安国雷、韩刚
 
## 1.2.15
 * 增加array类型验证，目前只是检测是否为数组 -邵王镇
 
## 1.2.13
 * validateFilter修复，设置key但未设置val导致warning提示、修复默认值弱类型检测错误导致0无效问题，设置enum传入类型in_array类型不严谨问题 -秘静雅
 
## 1.2.12
 * validateFilter增加数组批量添加规则功能 -秘静雅 长龙

## 1.2.11
 * 修复validateFilter 非必填没有default参数，有warning提示问题 -任成龙

## 1.2.10
 * 修复validateFilter 正则表达式不生效问题 -任成龙

## 1.2.9
 * 配合中台架构规范化，validateFilter开启自定义错误码功能addRuleEx -邵王镇

## 1.2.8
 * 增加自定义错误信息及错误码，用户可以定义错误提示文字内容及错误码,addRuleEx函数遗弃 -杜超群 徐长龙

## 1.2.7
 * 过滤后参数，不自动转换，防止数据精度丢失 -伟达
 * validator 验证功能失效修正 -秘静雅
 * validatorFilter 参数传入数组被转义问题 -尹伟达

