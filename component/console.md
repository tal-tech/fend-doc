# 自定义命令行脚本

## 业务自定义脚本，类方式
此处提供一个demo，演示如何加载初始化框架，在命令行下执行逻辑脚本

```php

require_once(dirname(dirname(__FILE__)) . '/../init.php');
ini_set('memory_limit', '5120M');

class Demo extends \Fend\Fend
{

    /**
     * 脚本主体
     */
    public function test()
    {
        echo "it workd!\n";
    }
}

$method = &$_SERVER['argv'][1];
$app = new Demo();
if (method_exists($app, $method)) {
    $app->$method();
} else {
    die("method not found\n");
}

//启动脚本命令行 php demo.php test
//参数为类函数名称，可以做一些更灵活设计
//请根据业务需要进行设计
//请记得拦截exception异常
```

## 业务自定义脚本，直接实现

```php
require_once(dirname(dirname(__FILE__)) . '/../init.php');
ini_set('memory_limit', '2048M');

echo "it workd!\n";
```