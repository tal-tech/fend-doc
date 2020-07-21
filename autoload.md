# 类加载规则

## Composer Autoload

Fend目前使用是Composer PSR4 Autoload 

所有特殊加载规则可扩充在composer.json, 更改后执行composer update/install即可生效，推荐全部使用psr4标准
```json
{
    "autoload": {
            "psr-4": {
                "Fend\\":"Fend",
                "App\\":"app"
            }
        }
}
```

## 自动加载规范

| namespace前缀 | 简介 |
| :--- | :--- |
| * | PSR4标准，要求所有文件名内只能一个类，类名大小写和文件大小写相同, 只要声明调用时会根据namespace+类名查找 |
| \app\Http | Controller请求入口，此内文件必须首字母大写，其他部分为小写 |
| \app\Config\ | 配置加载指定文件名，不指定扩展名，读取和文件名大小写必须匹配 |

## 自动加载的性能问题

PHP的autoload会在找不到用户调用类声明时，根据类名在目录下猜测查找类文件，期间会多次检测file_exist。 

这样会导致接口工作效率下降，使用如下命令执行后，会改善此问题

```bash
bin/optimizeComposer.sh
```

注意：建议在发版时做这个操作，平时开发切勿使用。composer一旦dump后会大小写不敏感，上线没有执行可能会导致故障

此操作核心原理为，将项目内所有类都在一个map内登记，如果不存在直接拒绝，减少file_exits操作

更多性能优化信息可参考 [性能优化](optimize.md)