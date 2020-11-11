# 错误处理规范

## 为什么使用Exception方式

为了加深印象，特别举例以供参考

目前PHPer开发人员有几种习惯去返回错误，常见如下：

### return False\|NULL

```php
if(!$mysql->real_connect('xxx','xxx')){
    return FALSE;
}
```

缺点：业务使用时，需要对返回值进行===FALSE进行判断。错误原因较难获取，只能通过查看error\_log或打断点获取故障原因，而返回结果和错误标志较难区分，使用时容易出误会，同时常见配合此类方式的还会使用gloabl变量保存错误原因及code。不能知道具体哪里或业务逻辑报错。

### return array\('code'=&gt;-123,'msg'=&gt;'reason'\)

```php
if(!$mysql->real_connect('xxx','xxx')){
    return array('code'=> $mysql->getErrorNumber(),'msg'=> $mysql->getMsg());
}
```

缺点：维护较复杂，如果代码层级深，需要将错误信息一层层传递到顶层controller或service内方可展示给用户（因为接口输出结果格式由controller和service层决定）并且无法第一时间得知是哪个文件抛出的故障。

### throw new Exception\(\)

```php
if(!$mysql->real_connect('xxx','xxx')){
    throw new Exception('code'=> $mysql->getErrorNumber(),'msg'=> $mysql->getMsg());
}
```

缺点：比较暴力的方式，所有地方需要try catch才能保证页面不会因为exception白屏，500。但是暴露问题很方便，故障可以很快通过backtrace获取到事发地点。

推荐使用throw Exception方式，方便简单，不用一级级传递，能够快速定位到错误点。至于Exception及500错误可以使用set exception handle进行最后一步拦截并记录错误日志即可

注意：除非业务需要尽量不要在代码内实现try cache拦截异常，否则会导致底层异常被拦截不易追踪

---

## 框架异常处理措施

目前可以在init.php内定义了register\_shutdown\_function 

此函数在项目退出时会自动触发函数对最后一次错误信息（使用error\_get\_last\(\)函数，命令行也可工作）对错误信息进行收集整理，若当前请求在fpm的debug模式下，会在请求结尾输出具体debug信息到返回值

## Exception类型
 > Fend对Exception做了分类封装，异常类放在 \Fend\Exception\ 内
 
|异常文件|用途|
|:---|:---|
| BizException.php | 业务异常，继承FendException，业务逻辑错误异常都继承此类 |
| FendException.php | 框架内所有异常都会继承 |
| SystemException.php |系统异常错误，继承FendException|