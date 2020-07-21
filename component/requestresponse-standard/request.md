# Request 请求类

Request类提供：
 * 获取用户请求参数
 * 获取系统环境变量
 * 获取请求Header
 * 获取请求Cookie
 * 获取上传文件
 * 获取Http Body Raw

## 如何获取到Request对象
app\Http\ 下Controller被调用时只要声明时附带Request $request, Response $response即可直接获取Request对象
```php
public function index(Request $request, Response $response)
{
    $user_id = $request->post("user_id", -1);
}
```

## 通过DI获取Request对象
```php
use Fend\Di;
Di::factory()->getRequest()->post("user_id", -1);
```

## Request使用示例
```php
//请求对象创建，创建后会自动从fpm环境变量中获取所有参数，并加工成统一格式
$request = new \Fend\Request("fpm");

//获取post参数 $_POST["user_id"], key不区分大小写
$user_id = $request->post("user_id");
if($user_id === '') {
    throw new \Fend\Exception\BizException("请填写用户id",-23);
}

//获取get参数，如果没有返回默认值-1 $_GET["user_id"], key不区分大小写
$user_id = $request->get("USER_ID", -1);
if($user_id === -1) {
    throw new \Fend\Exception\BizException("请填写用户id",-23);
}

//获取所有post参数 $_POST, key不区分大小写
$post = $request->post();

//获取post|get参数，post会覆盖get同名变量内容，同$_REQUEST，但是不支持全部获取, key不区分大小写
$request->getParam("user_ID", -2);

//获取所有request内数据，post优先于get参数，同$_REQUEST，不填写返回全部, key不区分大小写
$request->request("user_id");

//人工修改request内内容，如指定name那么只修改指定key $_GET , key不区分大小写
$request->setQueryString($get, $name = '');
$request->setPost($get, $name = '');

//获取服务器环境变量或所有变量 , key不区分大小写 $_SERVER
$request->server($name = "")；

//获取指定cookie或所有cookie $_COOKIE key不区分大小写
$request->cookie($name = "")；

//获取所有上传文件$_FILE
$request->file($name = "")；

//获取请求body内内容，file_get_contents("php://input")
$request->getRaw()；

//fpm下，用户传递过来的header都会增加HTTP_前缀，这里获取到的header都是带http前缀的, name变量不区分大小写
$request->header($name = '')；

//判断当前请求是否为Ajax请求
//判断依据 header("X-Requested-With") === "XMLHttpRequest"
$request->isAjax()；

```

## 参数过滤
支持过滤的函数有
 * $request->post
 * $request->get
 * $request->getParam
 * $request->request
 
以上第三个参数传递过滤选项即可，目前支持的过滤如下
 * html 去掉无用节点，去掉脚本、css、预防xss，需要依赖
 * special 过滤不可见字符
 * string 单引号双引号，unicode &
 * url 非网址合法字符过滤
 * email 非email合法字符过滤
 * float 浮点非法字符过滤
 * int 非法字符过滤
 * 不填 原样返回
 
 注意：Html过滤需要 composer require ezyang/htmlpurifier 4.12