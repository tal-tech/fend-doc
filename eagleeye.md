# 分布式链路跟踪原理篇
 分布式链路跟踪日志标准能将分布在不同服务器的一次请求的日志串联起来，按顺序展示，方便分布式服务定位排查问题。   
 
 本日志规范对常见日志字段进行了定义，能够方便倒入ELK系统进行分析，并提供扩展字段方便用户扩展. 
 
 Fend已经内置此标准在关键节点都会自动生成日志，属于一种特殊的埋点技巧，方便多维度分析
 
 与市面Trace不同，此方式颇多妙处、为作者心血、请自行体验

## 更新历史
 * 2017-11-01 增加x_department 必填，用于记录哪个部门提交的日志

#### TraceId 使用规则
 * 用户请求一个接口，接口入口生成一个唯一标识TraceId（可使用UUID）
 * 被用户调用接口及依赖资源 运行时产生的日志附带此TraceId
 * 被调用的接口、资源等 能够从被调用的请求中获取到TraceId，并在后续日志生成时附带到日志内（Header或参数或RPC的context）

#### TraceId 应用场景
 * 可通过TraceId能够将某次请求所有日志汇集，方便查询分析，回放
 * 可通过对标准格式的日志内容进行统计分析，可以分析出所有时间段内接口、资源的响应情况
 * 可通过对错误日志进行汇总去重，实现运行异常发现、排查、现场回放、警报


#### Traceid 生成规则 选择一个
简单版 GUID 依赖基础库，是否唯一不一定  
奢侈版 IP\_PID\_TIMESTAMP\_RAND 生成串较长  
精简版 IDC\_IP2LONG(C.D)\_TIMESTAMP-2017\_RAND=int 64 bit 各个语言开发难度大一点  

返回结果返回rpcid及header
### 层级计数器 RPCID(ID) 
<pre>
<code>一次系统调用的日志可以通过TraceId查找到，但是日志是乱序的  
通过RPCID可以记录日志的执行顺序及层级调用关系（并行请求按下任务顺序排法）  
如：  
   入口的RPCID 0.1
      发送http   					RPCID:0.1  
      查询mysql  					RPCID:0.2
      发送Http   					RPCID:0.3
      		被请求API 					RPCID:0.3.1
      		HTTP请求到另外一个API 		RPCID:0.3.2
      			被请求请求的API 			RPCID:0.3.2.1
      			被请求请求的API查数据库 	RPCID:0.3.2.2
      		被请求API继续处理业务 		RPCID:0.3.3
      继续处理后续业务  			RPCID:0.4

      ...略
     
</code>
</pre>

> 从上面实例可以看出RPCID，RPCID只有最后一位+1，前几层不变  
> 子调用的时候会增加层级计数 一个点代表一个调用深度层级的执行顺序。


## 请求接口附带参数
建议http请求附加在header，其他请求再讨论  

参照代码位置：fend/src/Server/Http.php   类文件 function  onRequest 70行-110行实行
 
 * traceid   
 * rpcid 

##采样规则及范围
日志量如果过大会导致接口性能下降，而采样无法回放每一次请求细节对于低概率故障无效。  

现行采用关键点记录方式：仅记录依赖资源请求日志，辅以用户自定义日志完成全部相关信息回放故障调查。
   
具体记录日志内容范围如下：

 * 1.Mysql请求IP、连接耗时、响应耗时、SQL模板、SQL附加参数*、执行错误、连接错误
 * 2.Http请求IP、URL、method、参数、httpcode、dns解析时间、连接时间、业务处理时间、执行错误、连接错误
 * 3.Redis 连接耗时、连接错误、执行错误、同上
 * 4.Memcache 连接耗时、连接错误、执行错误、同上
 * 5.框架未拦截Exception，对其去重汇总整理，可以回放其中几次调用细节
 * 6.框架内嵌的分级日志，用于手动记录日志
 * 7.手动埋点性能日志
 * 8.警报日志，一旦发现马上会去重汇总发送警报邮件
 * 9.每次请求的耗时，如果是核心系统，建议记录返回值


-------------

## 日志格式标准篇

> 此文档用于规定：TAL分布式跟踪系统的接入日志格式，通过这个日志可以规定日志的格式


### Logs Name enum
日志name字段规定，允许出现以下选项

* request.info 当前被请求接口的相关信息，如被请求接口，耗时，参数，返回值，客户端信息
* mysql.connect mysql连接时长
* mysql.connect.error mysql链接错误信息
* mysql.request mysql执行查询命令时长及相关信息
* mysql.request.error mysql操作时报错的相关信息
* redis.connect redis 链接时长
* redis.connect.error redis链接错误信息
* redis.request redis执行命令
* redis.request.error redis操作时错误
* memcache.connect
* memcache.connect.error
* memcache.request.error
* http.get 支持restful操作get put delete 
* http.post
* http.*.error
* 其他需讨论定制

#### 分级日志  
在以上字段基础上，提供分级日志拦截服务

* log.debug: debug log
* log.trace: trace log
* log.notice: notice log
* log.info: info log
* log.error: application error log
* log.alarm: alarm log
* log.exception: exception log


### Module name define

模块名称定义建议，用于区分当前日志所在模块层级、例如：  
app\_module\_class\_function
 
### Version
language\_clientname\_version  
cpp\_classclient\_0.1  
java\_interaction\_1.2  

### TimeStamp
用于标注当前日志记录时间，无须小数  
格式：unix Timestamp second int

### Duration
用于记录当前操作耗费时间  
float 1.3234 microtime

### Pid
进程id 如果是线程则pid\_tid 格式

### UID
用户uid、字符串

## 日志格式样例说明
 * 一条日志一个json
 * 日志使用name作为日志类型标识，module作为模块标识，辅以文件名（或类名）行数定位日志点
 * 除了duration timestamp 时间类字段需要做度量聚合对比的数据外 其他kv建议都为string类型，字符串如果传入的是数组请使用json序列化
 * 数据格式分为，基础信息root，固定数据区域data，附加自定义数据区域extra
 * 基础信息：包含traceid、时间、耗时等信息、这些项目将会作为主要查询依据
 * 固定数据区域：规定了每个请求记录的项目，这些项目将会作为主要查找依据
 * 附加自定义数据区域：这里放用户自定义数据信息

### 服务端日志 全量字段功能介绍，字段根据实际需要进行选择
```json
{
    "x_name": "string:全量字段介绍,必填，用于区分日志类型",
    "x_trace_id": "string:traceid，必填",
    "x_rpc_id": "string:rpcid，服务端链路必填，客户端非必填",
    "x_department":"部门缩写如tal_client_frontend 必填",
    "x_version": "string:当前服务版本 cpp-client-1.1 php-baseserver-1.4 java-rti-1.9，建议都填",
    "x_timestamp": "int:日志记录时间，单位秒，必填",
    
    "x_duration": "float:消耗时间，浮点数 单位秒，能填就填",
    "x_module": "string:模块路径，建议格式应用名称_模块名称_函数名称_动作，必填",
    "x_source": "string:请求来源 如果是网页可以记录ref page，选填",
    "x_uid": "string:当前用户uid，如果没有则填写为 0长度字符串，可选填",
    "x_pid": "string:进程pid，如果没有填写为 0长度字符串，如果有线程可以为pid-tid格式，可选填",
    "x_server_ip": "string 当前服务器ip，必填",
    "x_client_ip": "string 客户端ip，选填",
    "x_user_agent": "string curl/7.29.0 选填",
    "x_host": "string 链接目标的ip及端口号，用于区分环境12.123.23.1:3306，选填",
    "x_instance_name": "string 数据库连接配置的标识，比如rti的数据库连接，选填",
    "x_db": "string 数据库名称如：peiyou_stastic，选填",
    "x_code": "string:各种驱动或错误或服务的错误码，选填，报错误必填",
    "x_msg": "string 错误信息或其他提示信息，选填，报错误必填",
    "x_backtrace": "string 错误的backtrace信息，选填，报错误必填",
    "x_action": "string 可以是url、sql、redis命令、所有让远程执行的命令，必填",
    "x_param": "string 通用参数模板，用于和script配合，记录所有请求参数，必填",
    "x_file": "string userinfo.php，选填",
    "x_line": "string 232，选填",
    "x_response": "string:请求返回的结果，可以是本接口或其他资源返回的数据，如果数据太长会影响性能，选填",
    "x_response_length": "int:相应内容结果的长度，选填",
    "x_dns_duration": "float dns解析时间，一般http mysql请求域名的时候会出现此选项，选填",
    "x_extra": "json 放什么都可以,用户所有附加数据都扔这里"
}
```

### 客户端日志 全量字段功能介绍，字段根据实际需要进行选择
```json
{
   "x_name": "string:全量字段介绍,必填，用于区分日志类型，下面有具体的日志类型介绍",
   "x_trace_id": "string:traceid，uuid规则生成的串每次请求产生一个，请求失败重试请用同一个traceid必填",
   "x_department":"tal_client_frontend 必填",
   "x_rpc_id": "string:rpcid，服务端链路 非必填，如果客户端想追查一个调用牵扯的模块链路可以填写",
   "x_version": "string:当前服务版本 cpp-client-1.1 php-baseserver-1.4 java-rti-1.9，用于标识哪个客户端产生的日志，必填",
   "x_timestamp": "int:日志记录时间，单位秒，必填",
   "x_module": "string:模块路径，建议格式应用名称_模块名称_函数名称_动作，用来记录产生日志的模块所在位置，方便出现错误排查 必填",
   "x_action": "string 可以是url、sql、redis命令、所有让远程执行的命令，也可以是websocket指令等，在请求外部资源时必填",
   "x_param": "string 通用参数模板，用于和script配合，记录所有请求参数，在请求外部资源时必填",
   
   "x_msg": "string 错误信息或其他提示信息，选填，报错误必填，没有错误可以留空",
   "x_backtrace": "string 错误的backtrace信息，选填，报错必填，没有错误时可以为0",
   "x_duration": "float:消耗时间，浮点数 单位秒，可选填，填写后，可以针对这些操作进行性能统计",
   "x_uid": "string:当前用户uid，如果没有则填写为 0长度字符串，可以根据uid进行判断用户或教室唯一性，建议客户端uuid或者用户uid 可选填",
   "x_pid": "string:进程pid，如果没有填写为 0长度字符串，如果有线程可以为pid-tid格式，用于区分多进程情况，可选填",
   "x_source": "string:请求来源 如果是网页可以记录ref page，如果是被调用可以记录请求服务等信息，选填",
   "x_server_ip": "string 当前服务器ip，选填",
   "x_client_ip": "string 客户端ip，选填",
   "x_user_agent": "string curl/7.29.0 选填",
   "x_host": "string 链接目标的ip及端口号，用于区分环境12.123.23.1:3306，选填",
   "x_instance_name": "string 数据库连接配置的标识，比如rti的数据库连接，选填",
   "x_db": "string 数据库名称如：peiyou_stastic，选填",
   "x_code": "string:各种驱动或错误或服务的错误码，选填，报错误必填",
   
   "x_file": "string userinfo.php，选填",
   "x_line": "string 232，选填",
   "x_response": "string:请求返回的结果，可以是本接口或其他资源返回的数据，如果数据太长会影响性能，选填",
   "x_response_length": "int:相应内容结果的长度，选填",
   "x_dns_duration": "float dns解析时间，一般http mysql请求域名的时候会出现此选项，选填",
   "x_extra": "json 放什么都可以,用户所有附加数据都扔这里"
 }
```
### Access Log
服务被请求日志，包括被请求参数，耗时，返回值，来源等信息
```json
{
    "x_name": "request.info",
    "x_trace_id": "123jiojfdsao",
    "x_rpc_id": "0.1",
    "x_version": "php-baseserver-4.0",
    "x_department":"tal_client_frontend",
    "x_timestamp": 1506480162,
    "x_duration": 0.021,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_game1_start",
    "x_user_agent": "string curl/7.29.0",
    "x_action": "http://testapi.speiyou.com/v3/user/getinfo?id=9527",
    "x_server_ip": "192.168.1.1:80",
    "x_client_ip": "192.168.1.123",
     "x_param": "json string",
    "x_source": "www.baidu.com",
    "x_code": "200",
    "x_response": "json:api result",
    "x_response_len": 12324
}
```

### Mysql连接日志
可以用于分析连接性能
```json
{
    "x_name": "mysql.connect",
    "x_trace_id": "123jiojfdsao",
    "x_rpc_id": "0.2",
    "x_version": "php-baseserver-4",
    "x_department":"tal_client_frontend",
    "x_timestamp": 1506480162,
    "x_duration": 0.024,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_mysql_connect",
    "x_instance_name": "default",
    "x_host": "12.123.23.1:3306",
    "x_db": "tal_game_round",
    "x_msg": "ok",
    "x_code": "1",
    "x_response": "json:****"
}
```

### Mysql 请求日志
可以用于分析Mysql查询性能
```json
{
    "x_name": "mysql.request",
    "x_trace_id": "123jiojfdsao",
    "x_rpc_id": "0.2",
    "x_version": "php-4",
    "x_department":"tal_client_frontend",
    "x_timestamp": 1506480162,
    "x_duration": 0.024,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_game1_round_sigup",
    "x_instance_name": "default",
    "x_host": "12.123.23.1:3306",
    "x_db": "tal_game_round",
    "x_action": "select * from xxx where xxxx",
    "x_param": "json string",
    "x_code": "1",
    "x_msg": "ok",
    "x_response": "json:****"
}
```

### Curl请求日志
可以用于分析依赖服务性能
```json
{
    "x_name": "http.post",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_game1_round_win_report",
    "x_action": "http://testapi.speiyou.com/v3/game/report",
    "x_param": "json:",
    "x_server_ip": "192.168.1.1",
    "x_msg": "ok",
    "x_code": "200",
    "x_response_len": 12324,
    "x_response": "json:responsexxxx",
    "x_dns_duration": 0.001
}
```
### Memcache连接日志
可以用于分析连接性能
```json
{
    "x_name": "redis.connect",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_game1_round_win_report",
    "x_db_link_name": "default",
    "x_host": "12.123.23.1:3306",
    "x_db": "0",
    "x_code": "1",
    "x_msg": "json:****",
    "x_dns_duration": 0.001
}
```

### Memcache连接日志
可以用于分析连接性能
```json
{
    "x_name": "memcache.connect",
    "x_trace_id": "123jiojfdsao",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "js_memcache_connect",
    "x_db_link_name": "default",
    "x_host": "12.123.23.1:3306",
    "x_code": "1",
    "x_msg": "json:****",
    "x_dns_duration": 0.001
}
```

### Log 分级日志 Info级别
```json
{
    "x_name": "log.info",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "game1_round_win_round_end",
    "x_file": "userinfo.php",
    "x_line": "232",
    "x_msg": "ok",
    "x_code": "201",
    "extra": "json game_id lesson_num  xxxxx"
    
}
```

### Exception 异常日志
```json
{
    "x_name": "log.exception",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "game1_round_win",
    "x_file": "userinfo.php",
    "x_line": "232",
    "x_msg": "exception:xxxxx call stack",
    "x_code": "hy20001",
    "x_backtrace": "xxxxx.php(123) gotError:..."
}
```
### 警报日志
需要跟日志监控组合作抓取去重报警

```json
{
    "x_name": "log.alarm",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_duration": 0.214,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "game1_round_win_round_report",
    "x_file": "game_win_notify.php",
    "x_line": "123",
    "x_msg": "game report request fail! retryed three time..",
    "x_code": "201",
    "x_extra": "json game_id lesson_num  xxxxx"
    
}
```

### matrix 统计日志
类似zabbix openFalcon的模块性能统计
```json
{
    "x_name": "matrix.count",
    "x_trace_id": "123jiojfdsao",
    "x_department":"tal_client_frontend",
    "x_rpc_id": "0.3",
    "x_version": "php-4",
    "x_timestamp": 1506480162,
    "x_uid": "9527",
    "x_pid": "123",
    "x_module": "game1_round_win_click",
    "x_extra": "json curl invoke count"
    
}
```
