# Fend Env组件

## 简介
Env 环境变量配置，可以设置一些隐私的配置，通过环境来区分。还可以设置 `CONFIG_NAME` 来加载子目录配置。

## 安装

 ```bash
 composer require php/fend-plugin-env
 ```

## 使用方法
- 新建一个配置文件。默认放在项目根目录，命名 .env (名称也可以自定义)

```ini
CONFIG_NAME="test"
APP_NAME="fend-demo"
```

> 实例中配置 `CONFIG_NAME="test"` 实际 `Config` 加载 `app/Config/test` 目录。
### 在 init.php 加载配置文件

```php
<?php
//注释掉原来变量 ,注意如果没有使用 CONFIG_NAME="test" 选项，不要注释
//\Fend\Config::setConfigPath(SYS_ROOTDIR . 'app/Config');

//开启根据环境加载指定目录配置
\Fend\Env::load(SYS_ROOTDIR);

```

### 获取配置
```php
<?php
$app_name = env('APP_NAME', 'fend-demo');
var_dump($app_name);
```

> 配置文件使用 `parse_ini_file` 解析，所以配置文件格式遵循 `parse_ini_file` 要求即可。