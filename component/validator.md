# Fend Validator组件
Validator组件是Fend框架验证参数类合集，目前主要有两款：
  * ValidateFilter 新封装的参数统一校验类，能够统一登记参数验证规则（必填、数据类型、取值范围、默认值、自定义闭包规则），支持类似swagger API文档导出（建设中）
  * Validator 老款仿造Thinkphp实现的validator，对输入参数多规则验证，目前没有在维护，后续会投入精力进行整理
  * Validation 模仿laravel规则制作的参数验证组件

## 引入组件
```bash
composer require php/fend-plugin-validator
```

## Validation 使用样例
```php
//示例1：演示数组内数据字段及组合规则
//设置过滤规则
$rules = [
    'isadmin' => 'require|bool',
    'user.info.*.id' => 'require|int', //子数组 检测
    'user.info.*.name' => 'require|string|length:2,16',
];

//获取请求参数
//$param = $request->get();

//输入数据demo
$param = [
  "isadmin" => 0,
  "user"=> [
                "info"=>[ 
                            ["id"=>1,"name"=>"haha"],
                            ["id"=>2,"name"=>"haha2"],
                        ]
           ]
];

//验证结果，如果异常error下标会有具体错误信息
$result = Validation::make($param, $rules, []);

//除了常规 xx|xx 字符串规则，validation还支持数组方式
//规则支持数组传递，key为规则，value为附加参数，可用于正则和自定义回调定义
$rules = [ 
           'field1' => [ 
                         "same" => [
                                        "a", 
                                        "b",
                                    ]
                        ] 
        ];

//正则中常有|容易和字符串分隔符混淆，如需要可以使用数组方式设置规则如下
[
    'isadmin' => 'require|bool',
    'user.info.*.id' => 'require|int', //子数组 检测
    'user.info.*.name' => 'require|string|length:2,16',
    'stu_cou_ids' => ["require","regex" => ["/^[\d,]*$/"]],
]
```

#### Validation 支持规则
```php
/**
 *  require     设置为必填
 *  default     未填写设置默认值

 *  bool    是否是bool类型，true false "true" "false" 0 , 1 , "0", "1"
 *  number  是否是数值类型
 *  string  是否是字符串类型
 *  array   是否是数组

 *  json        合法json
 *  email       合法邮件格式
 *  alpha       字符为全英文字符组成
 *  alpha_num   字符为英文字符、数字组成
 *  alpha_dash  英文字符数字-_符号组成
 *  url         内容为合法网址
 *  timestamp   内容为合法时间戳，目前只是检测是否为数值、正数，支持毫秒timestamp
 *  date        内容可以被str2time转换
 *  ip          内容为合法ip，支持v4 v6
 *  ipv4        内容为合法ipv4
 *  ipv6        内容为合法ipv6
 *  start_with  内容以指定字符串开始
 *  end_with    内容以指定字符串结束
 *  in_str      内含指定字符串、目前只支持一个参数

 *  min:m           必须大与指定值
 *  max:m           不能大于指定值
 *  range:m,n       数值取值范围（含）
 *  length:m,n      字符串字节长度
 *  count:m         array数组最大子项个数
 *  in:x,y,z..      内容必须在规定枚举内，强类型匹配
 *  not_in:x,y..    内容不在规定列表内，强类型匹配，非string类型必须使用数组指定xyz
 *  regex:/[0-9]+/  执行自定义正则表达式验证，需要数组方式设置规则

 *  callback =>function($param){} 自定义回调处理异常，需要数组方式设置规则
 *  require_if:anotherField,value1,value2...      如果指定的其它字段（ anotherfield ）等于任何一个 value 时，被验证的字段必须存在且不为空。
 *  require_with:field1,field2...       只要在指定的其他字段中有任意一个字段存在时，被验证的字段就必须存在并且不能为空。
 *  require_with_all:field1,field2...       只有当所有的其他指定字段全部存在时，被验证的字段才必须存在并且不能为空。
 *  require_without:field1,field2..        只要在其他指定的字段中有任意一个字段不存在，被验证的字段就必须存在且不为空。
 *  require_without_all:field1,field2...    只有当所有的其他指定的字段都不存在时，被验证的字段才必须存在且不为空。
 *  same:field1,field2...                    给定字段必须与验证的字段匹配。
 */
```

## ValidatorFilter 使用样例
```php
<?php
//演示输入参数
$param = [
    "must"     => true,
    "string"   => "wahahah",
    "int"      => "3244",
    "float"    => "43.6",
    "double"   => "123.1",
    "email"    => "test@qq.com",
    "enum"     => "yes",
    "callback" => "ahaha",
];
//创建验证类，提供当前接口网址及功能介绍，及获取参数方式method（GET\POST等）
//当请求本接口附带tal_sec=show_param_json时，会输出下面录入的所有参数信息json格式，用于生成wiki
$validate = new \Fend\ValidateFilter("http://www.test.php/user/info", "根据学生id查找学生信息", "get");

//用于生成接口文档返回结果，demo请求参数，可以提供多组，每组体现不同情况
$validate->addDemoParameter([["uid" => 12312], ["uid" => 123]]);

//接口参数规则
//bool检测
$validate->addRule("must", "bool", "bool类型，必填字段", true);
//int检测，只是检测内容是否都为数字，类型不检测，不转换预防超长int被转换溢出
$validate->addRule("default", "int", "int类型，非必填，默认1", false, 1);
//字符串检测
$validate->addRule("string", "string", "用户uid", false, "", [1, 10]);
$validate->addRule("int", "int", "用户uid的int写法", false, "", [1, 20000]);
//浮点检测，返回结果不转类型成float防止丢失精度
$validate->addRule("float", "float", "float类型", false, "", [1, 20000]);
$validate->addRule("double", "double", "double", false, "", [1, 20000]);
$validate->addRule("email", "email", "email检测", false);
//用户输入项，必须是可选范围内选项
$validate->addRule("enum", "enum", "enum检测:yes代表xx，no代表xx", false, "", ["yes", "no"]);

//闭包自定义参数校验规则
$callback = function ($key, $val) {
    if ($val != "ahaha") {
        throw new \Exception("嗯错误了");
    }
    return $val;
};
$validate->addRule("callback", "callback", "用户回调规则", false, "", $callback);

//callable 方式 直接指定类名和函数名
//$validate->addRule("t", "callback", "邮件正则检测", true,"",[validatefilterTest::class,"t1"]);

//自定义错误提示信息及错误码
$message = [
            "must.require" => ["must必填,自定义哈",223],
            "string.int" => "user_id 必须是数值",
            "float.float" => ["只要float", 3636], //数组类型，第二个参数为自定义exception错误码
            "regx.regx" => "regx 必须由数字英文字符串组成",
            "user_name.string" => "user_name 必须是字符串类型数据",
 ];
$validate->addMessage($message);

try{
    //$param为演示数据，建议实际使用传递\Fend\Di::Factory()->get("Request")->get(); || \Fend\Di::Factory()->get("Request")->post();
    $result = $validate->checkParam($param);
}catch(\Exception $e) {
    return Di::factory()->getResponse()->json(["state"=> 0, "msg" => $e->getMessage(), "code" => $e->getCode()]);
}

//返回所有符合条件的变量，包括没有传递但是有默认值参数
var_dump($result);
?>
```

### 数组批量添加规则
```php
<?php

$rules = [
    "must" => ["bool", "bool类型，必填字段", true],
    "default" => ["int", "int类型，非必填，默认1", false, 1],
    "string" => ["string", "用户uid", false, "", [1, 10]],
    "int" => ["int", "用户uid的int写法", false, "", [1, 20000]],
    "float" => ["float", "float类型", false, "", [1, 20000]],
    "double" => ["double", "double", false, "", [1, 20000]],
    "email" => ["email", "email检测", false],
    "enum" => ["enum", "enum检测:yes代表xx，no代表xx", false, "", ["yes", "no"]],
    "heiheihei" => ["string", "非必填，没填写", false],
    "testreg" => ["regx:/^[_a-z0-9-]+(\.[_a-z0-9-]+)*@[a-z0-9-]+(\.[a-z0-9-]+)*(\.[a-z]{2,})$/", "邮件正则检测", true],
    "t" => ["callback", "邮件正则检测", true, "", [validatefilterTest::class, "t1"]],
    "callback" => ["callback", "用户回调规则", false, "", $callback],
];

$validate->addMultiRule($rules);

?>

```

## Validator 使用样例
类Thinkphp方式的validator，目前还在整理阶段
```php
<?php
 $rules = array(
    'name' => 'alpha|required', 
    'age'=> 'num--只能是数字|required--必须的', 
    'color' => 'optional--非必填|min:5--最小长度为5'
 );
 list($valdi_data,$errors) = validate($_POST, $rules);
```