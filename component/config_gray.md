# 灰度压测

## 灰度影子库开启规则
灰度模式开启触发条件如下：
 * 当请求header内traceid值前缀为pts_则会自动开启灰度影子库模式 
 * 当请求带header Xes-Request-Type: performance-testing 时自动开启灰度模式
 * 人工设置灰度开关 EagleEye::setGrayStatus(true);  EagleEye::setGrayStatus(false);

## 影子库支持组件
目前支持影子库功能的插件如下:
 * `fend-core` EagleEye::getGrayStatus 获取灰度状态、
 * `fend-plugin-mysqli` 灰度压测状态时 自动从配置查找后缀-gray配置，如果有自动使用此配置
 * `fend-plugin-pdo` 灰度压测状态时 自动从配置查找后缀-gray配置，如果有自动使用此配置
 * `fend-plugin-redis` 灰度压测状态时 自动从配置查找后缀-gray配置，如果有自动使用此配置
 * `fend-plugin-kafka` 灰度压测状态时 自动从配置查找后缀-gray配置，如果有自动使用此配置
 * `fend-plugin-rabbitmq` 灰度压测状态时 自动从配置查找后缀-gray配置，如果有自动使用此配置

## 灰度状态检测
```php
use \Fend\Log\EagleEye;

//获取灰度状态
$isenable = EagleEye::getGrayStatus();
```
