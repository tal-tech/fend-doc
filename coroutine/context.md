# 协程上下文

## 变量作用域规范

Fend框架对于变量的作用域做如下规范，如果用户要使用时务必注意：
 * `框架级全局变量`：框架内所有 `$GLOBAL` 及 `static`  定义的变量
 * `请求级全局变量`：使用`RequestContext`类进行统一管理 如:每次请求获取到的Request、Response、debug标志
 * `协程级全局变量`：使用`Context`类进行统一管理 如：当前协程下需要暂存的对象和数据可以通过此类set\get

## $GLOBAL|static 框架级全局变量
所有static及$GLOBAL内变量对于程序来说是共享的, 多个请求可以全局变量修改读取 
  
常用于 存储框架配置，连接池管理，依赖注入`DI` set\get

在框架内任何地方皆可修改读取，使用时要确认是否能够给多个请求同时共用

## RequestContext 请求级全局变量

在实际使用中，如某些特殊的值需要在整个请求生命周期内全局共享，需要使用特殊的上下文管理类 `RequestContext`

`RequestContext`组件提供以下API接口来操作上下文：

* `RequestContext::set(string $id, $value)`   设置值
* `RequestContext::get(string $id, $default = null)`  获取值
* `RequestContext::has(string $id)`   判断指定的key是否存在
* `RequestContext::override(string $id, \Closure $closure)` 覆写指定的key

在 `RequestContext` 中设置的值，在单次Request请求生命周期内，是全局共享的，即使在子协程中，也能访问并修改（也因为如此，请谨慎使用该功能）。

`根请求协程`处理完毕返回结果后会`自动释放` RequestContext内存储内容

## Context 协程级全局变量

由于同一个进程内协程间是内存共享的，但协程的执行/切换是非顺序的，也就意味着协程会像线程一样，存在同时读写static修饰的全局变量的问题。因此我们也需要像线程全局变量一样，在协程中引入协程全局变量这样一个概念。

在Fend框架中，我们通过 `Context` 组件来实现协程全局变量，也叫做协程上下文。

`Context`组件提供以下API接口来操作上下文：

* `Context::set(string $id, $value)`   设置值
* `Context::get(string $id, $default = null)`  获取值
* `Context::has(string $id)`   判断指定的key是否存在
* `Context::override(string $id, \Closure $closure)` 覆写指定的key

通过这些方法设置和获取的值，都仅限于当前的协程，在协程结束时，对应的上下文也会自动跟随释放掉，无需手动管理，无需担忧内存泄漏的风险。

### Context内的变量继承

上一小节中我们讲过，Context上下文是协程间隔离的，因此在父协程中存入Context的值，在子协程中是无法获取的。但是某些业务场景下，我们需要让子协程能够访问到父协程写入的值。因此，Context还提供了一下API用于实现此需求：

* `Context::setGlobal(string $id, $value)` 设置值
* `Context::getGlobal(string $id, $default = null)` 获取值

通过这两个API设置的上下文，在创建子协程的时候，会由底层自动将值拷贝后写入子协程的`Context`中，由此实现`Context`内容的继承传递。

需要注意的是，该方式写入的`Context`，是一份拷贝，因此无法在子协程中修改值后，在父协程中读到修改后的值（即继承是单向的）。 

另外只有使用Fend框架提供的创建协程函数来创建协程才能够继承获取到值

### 点分符 检索
Context内的内容都支持`点分符`检索如
```php

//假设Context内存储数据如下：
$data =[
 
    'Redis' => [
        'default' => [
            "host" => "127.0.0.1",
            "port" => 9999, 
        ],
        'nps' => [
             "host" => "127.0.0.1",
             "port" => 9999, 
         ],
    ]
];

//如想获取Redis->default内内容可以使用如下方式设置id
Contexnt::get('Redis.default');

//set、has、override皆支持此方式
```
