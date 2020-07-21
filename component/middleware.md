# 中间件
Fend目前为止并没有提供完全独立的中间件，只提供了请求和返回时的拦截

通过这个拦截，业务方可以实现中间件功能 

具体实现是在app\Http\内的所有Controller代码被请求触发之前，会执行类内Init函数（如果有），用户业务逻辑代码执行完毕后会执行UnInit

## Init 前置拦截

```php
public function Init(Request $request, Response $response); 
```

业务逻辑触发之前会调用如上函数，其中Request对象为请求信息提供类，Response用于返回结果 

如果想拦截后续操作不再执行，可以使用$response->break("要返回给客户端的字符串");

如果在此函数内产生任何异常，除UnInit函数外都将会被拦截不再继续执行  

注意：此函数名大小写敏感 


## UnInit 后置拦截 
```php
public function UnInit(Request $request, Response $response, &$result) 
```
业务执行完毕后，如果类内存在函数UnInit，会主动调用此函数 

其中Request对象为请求信息提供类，Response用于返回结果，result为业务返回结果，由于是引用类型用户可以直接对其内容进行更改 

```php
$result = json_encode($result);
```
注意：此函数名大小写敏感 

## 如何构建统一的拦截中间件
考虑到业务需求，业务方可以实现Controller基类，对Init及Uninit进行统一封装，所有所需标准化Controller继承即可实现多层级可定制化请求拦截

## 递归嵌套方式中间件Middleware

Fend框架提供了Middleware组件，用于在请求发生前和请求发生后执行用户自定义的逻辑。

Middleware组件采用嵌套递归调用的方式，多个Middleware会按顺序递归调用。

中间件配置目前在app\Config\Router.php内

`中间件的配置在路由配置内配置`

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

