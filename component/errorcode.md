# Fend ErrCode组件

## 功能说明

ErrCode组件用于提供统一的、按模块划分的错误码以及错误信息集合功能。通过简单的配置文件就可以实现错误码对应错误信息的统一管理。


## 配置

在`init.php`文件中， 指定位置加入以下代码：

```php

//设置配置，加载路径
\Fend\Config::setConfigPath(SYS_ROOTDIR . 'app/Config');

// 加入这一行
\Fend\Config::set('fend_err_code_file', SYS_ROOTDIR . 'app' . FD_DS . 'Const' . FD_DS . 'ModuleDefine.php');
```

## 错误码定义

在项目的`app`目录下新建`Const`目录，并新建文件`ModuleDefine.php`，文件内容如下：

```php
<?php


return [
    '001' => __DIR__ . '/User.php'
];

```

其中，`001` 代表模块错误码，`__DIR__ . '/User.php'` 代表该模块指定的错误码定义文件。

接着，在同级目录下，创建`User.php`文件，在其中填入如下内容：

```php
<?php


return [
    '0001'  => 'user %s not found',
    '0002'  => 'create user error'
];
```

其中，`0001` 是定义的错误码，后面的字符串为错误消息提示。（字符串定义遵循 sprintf 格式标注）

## 使用

定义好后，在业务代码中，就可以这样使用：

```php

use Fend\Exception\ErrCode;

// 抛出 001-0001错误，得到的错误码为 0010001，错误消息为"user 1 not found"
$userId = 1;
ErrCode::throws('001', '0001', [$userId]);

```

## 错误字典生成工具
generator 工具目前在fend-demo/bin内，使用此工具能够将项目中所有错误码生成一个错误字典文件，方便查阅 

命令使用方法:
```bash
./generator -a error -c ./App/Const/ModuleDefine.php -o ./output\n";
```