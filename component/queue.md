# fend-plugin-queue
此为队列服务统一工厂类，需要搭配对应的驱动工作，目前支持如下

## 使用
```php
$queue = Queue::factory(Queue::QUEUE_TYPE_RDKAFKA,"kafka配置下标");

```