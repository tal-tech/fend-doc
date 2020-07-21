# Response 响应类

Response响应类提供：
 * 设置返回Header
 * Cookie
 * HttpCode
 * 301 302 跳转
 * 中断后续执行返回结果
 * Header Content-type：Json 

## 如何获取到Response对象
app\Http\ 下Controller被调用时只要声明时附带Request $request, Response $response即可直接获取Response对象
```php
public function index(Request $request, Response $response)
{
    $response->header("Test", "testString");
}
```

## 通过DI获取Response对象
```php
use Fend\Di;
Di::factory()->getResponse()->header("Test", "testString");;
```

## Response使用示例
```php
//创建响应对象，自动从swoole中获取
$response = new \Fend\Response("swoole_http");

//设置返回header，注意kv的大小写
$response->header($key, $value);

//设置返回cookie
$response->cookie($key, $value = '', $expire = 0, $path = '/', $domain = '', $secure = false, $httponly = false);

//设置响应http code，404，500。302 301请用redirect设置
$response->status($httpCode);

//终止后续代码，跳转到指定网址，可以指定301、302
$response->redirect($url, $code = 302);

//返回将数据序列化成Json，并设置content-type为json，如果需要其他json附加选项，追加在参数后即可
$response->json($data, ...$option);

//终止后续代码执行，返回字符串结果给用户
$response->break(string $result);
```