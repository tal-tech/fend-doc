# 请求路由

### 路由模式
Fend目前支持两种路由模式，二选一或都开启。
 * FastRouter 正则路由模式 优先匹配，搜索不到继续Direct Map模式 （可选开启）
 * Direct Map 直接网址路径映射模式，性能最好 （可选开启）
 
路由优先级为FastRouter > DefaultRouter 

## 路由配置

 > 配置文件路径：/app/Config/Router.php

```php
return [

    //不同域名可以映射不同app\Http下子目录,若没有，默认以\app\http为开始查找
    //限制规则：app\Http 内文件名必须首字母大写，其他字母皆为小写
    "map" => [
        //默认请求访问的路径，所有未知域名请求都会使用此路由
        'default'     => [
            'root'       => "\\App\\Http",//namespace
            'direct'     => true,//如果没有router匹配，那么继续按路径进行路由
            "fastrouter" => false,//启用fastrouter
            'middlewares' => [              // 中间件功能
                \Example\Router\Middleware\TestMiddleware::class
            ]   
        ],
        //所有www.fend.com会走这个配置，注意非80端口网址必须写明端口，否则不会路由
        'www.fend.com' => [
            'root'       => "\\App\\Http\\Tal",     //会从这个namespace开始去搜索网址映射
            'direct'     => true,                   //如果没有router匹配，那么继续按路径进行路由
            "fastrouter" => true,                  //启用fastrouter，优先匹配这个路由，如果找不到会使用direct模式检索
            'open_cache'  => true,                  //线上开启routercache能够提高QPS，但是发版必须清理
            'middlewares' => [              // 中间件功能
                \Example\Router\Middleware\TestMiddleware::class
            ],
            //fastrouter映射
            'router'     => [
                ['GET', '/test1', '\App\Http\Index@index'],
                ['POST', '/test1', '\App\Http\User@getList'],
                ['GET', '/test1/{id:\d+}/{name}', '\App\Http\User@getInfo'],
            ],
        ],
    ],
];
```

## Direct Map 路由介绍
Direct Map 核心规则为，请求网址路由URI对应着对应类名搜索路径 ，如下

| 请求URI | 文件路径 | 类名 |
| :--- | :--- | :--- |
| /                       | app/Http/Index.php     | 类名\App\Http\Index |
| /user/info/getUserbyUid | app/Http/User/Info.php    |  \App\Http\User\Info->getUserbyUid |
| /user/info/             | app/Http/User/Info.php      | \App\Http\User\Info->Index |

注意：app/Http/下所有类必须首字母大写其他小写。 

## FastRouter 路由介绍
 > * FastRouter：[https://github.com/nikic/FastRoute](https://github.com/nikic/FastRoute)

注意：如果配置内open_cache开启了fastRouter 的 cache，Cache会生成在\app\Cache目录内，上线更新务必清理cache

## 中间件Middleware介绍

Fend框架提供了Middleware组件，用于在请求发生前和请求发生后执行用户自定义的逻辑。

Middleware组件采用嵌套递归调用的方式，多个Middleware会按顺序递归调用。

中间件配置目前在app\Config\Router.php内

```php
use Fend\Router\Middleware\RequestHandler;
use Fend\Router\Middleware\MiddlewareInterface;

class TestMiddleware implements MiddlewareInterface
{

    public function process($request, RequestHandler $handler)
    {
        // 获取本次请求的Controller和Action
        /**
         * @var $request Request
         */
        $info = $request->getController();
        $controller = $info['controller'];
        $function = $info['action'];

        var_dump(sprintf("call %s::%s", $controller, $function));
        
        // 执行下一个中间件或者控制器逻辑
        try {
            // 必须有这一行调用
            $result = $handler->handle($request);
        } catch (\Exception $e) {
            $result = $e->getMessage();
        }
        // 在这里可以处理返回结果
        var_dump(sprintf("with result: %s", $result));
        // 返回响应值给上一层
        return $result;
    }
}

```

这里我们简单定义了一个中间件，它可以捕获逻辑代码执行时发生的异常，并将返回结果定义为异常信息。