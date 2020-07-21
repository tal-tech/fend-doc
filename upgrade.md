## 升级指南

Fend除了自身在Composer外，还有一些文件是没有在版本管理内，此类文件一般都在fend-demo内, 升级时按以下步骤逐个替换即可.

本章节主要介绍版本升级的一些注意事项。

### 更新历史
2019-11-11 为了响应目录规范、Fend将项目根目录下文件夹都改为小写：
 * App -> app
 * Coverage -> coverage
 * Fend -> fend
 * Logs -> logs
 * Test -> tests
 * Bin -> bin
请后续升级将对应目录文件名同步改变

composer.json 
目录大小写改变升级后需要更新composer.json 

```json
{
  "autoload": {
        "psr-4": {
            "Fend\\":"./src",
            "App\\": "./App"
        }
    }
}
```
替换为
```json
{
  "autoload": {
        "psr-4": {
            "Fend\\":"./src",
            "App\\": "./app"
        }
    }
}
```

### init.php
文件位于项目根目录，用于框架初始化，拷贝覆盖即可，同时fend-core组件建议升级到最新版本

### www/index.php
FPM请求入口，升级时直接覆盖即可，同时fend-core组件建议升级到最新版本

### app/Config/Fend.php
框架日志配置及框架配置，经常性会有升级更新，建议定期对比查看，同时fend-core组件需升级到最新版本
