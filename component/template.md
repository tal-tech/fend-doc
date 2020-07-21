## 模板引擎

### 简介
目前Fend封装的模板引擎有两种：
 * Smarty
 * PHP原生模板引擎
 

## Smarty模板引擎
模板文件夹目前默认定义在\app\View内，模板使用tpl做为结尾，如果要更改这个模板位置可以修改init.php内 

注意：Smarty会使用Cache目录app\Cache目录，Cache记得定期清理，否则会导致线上故障 

```php
<?php
namespace App\Http;

use Fend\Template;
 
class User extends Template
{

    /**
     * 初始化模板
     */
    public function Init()
    {
        parent::initTemplate();
    }
    
    //展示网页
    public function showInfo()
    {
        $username = "xcl";
        $this->assign($username, "username"); //特别注意
        return $this->showView("index");
    }

}

```

## PHP原生模板

### 简介
模板文件夹目前默认定义在\app\View内 

利用PHP本身特性实现的原生模板 作者：@望乐乐 

### 使用方法
```php
<?php
namespace App\Http;

use Fend\PHPTemplate;

class User extends PHPTemplate
{

    /**
     * 初始化模板
     */
    public function Init()
    {
        parent::initTemplate();
    }

    //展示网页
    public function showInfo()
    {
        $username = "xcl";
        $this->assign("username", $username);
        return $this->showView("index");
    }

}
```
### 原生模板样例
app\View\index.tpl
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<body>
<?php include $this->tplDir . 'header.tpl'; ?>

<div>
    我是:<?php echo $this->username; ?>.
</div>

<?php include $this->tplDir . 'footer.tpl'; ?>
</body>
</html>
```
