# Redis驱动
Fend 原生提供的Redis驱动，底层支持自动重连，定期ping检测链接健康，key前缀


调用样例：

```php
$redis = \Fend\Cache::Factory(Fend_Cache::CACHE_TYPE_REDIS, "配置下标");
$redis->set("test", "yes");
echo $redis->get("test");
```

### 闭包填充数据
```php
//查询cache，若cache不存在调用回调填充数据并返回结果 by xialei
$data = $memcache->getOrSet('key', function() {
   // 查询数据库或其他操作...
   return ['userId' => 1];
}, 3600);
var_dump($data); // ['userId' => 1]
```

### Redis技巧封装
Redis技巧封装，提供锁，轻量队列（无ack），pipeline示例，另外又一个fend-plugin-redismodel组件是对rediskey进行统一管理的model和此组件同名但不同
```php
namespace App\Model;

use Fend\App\RedisModel;
use Fend\Cache;

class DemoRedisModel extends RedisModel
{
    protected $_config = "default";

    protected $_dbType = Cache::CACHE_TYPE_REDIS;

    public function recordUser()
    {
        $id = mt_rand(12222, 3333333);
        //获取互斥锁, 如果抢到，这个锁会在5秒后自动释放
        $ret = $this->lock("fend_test_" . $id, 5);
        if ($ret) {
            //开启pipline模式批量执行redis命令
            $this->startTransaction();

            $this->set("test_aaa", 123);
            $this->_db->incr("test_aaa");
            $this->_db->incr("test_aaa");
            $this->_db->incr("test_aaa");

            //执行pipline
            $result = $this->commitTransaction();
            //锁用完释放掉
            $this->unlock("fend_test_" . $id);
            return $result;
        }

        //锁用完释放掉
        $this->unlock("fend_test_" . $id);
        return false;
    }

    /**
     * 下发任务给redis Queue(list)
     * 仅供参考，不提供数据安全保障
     * @return bool|int
     */
    public function sendQueue()
    {
        return $this->pushQueue("fend_queue_test", "yes");
    }

    /**
     * 获取任务从redis queue(list)
     * 仅供参考，不提供数据安全保障
     * 没有数据会等待5秒，超过没有数据返回false，有数据马上返回
     * @return array|false|string
     */
    public function getQueue()
    {
        return $this->popQueue("fend_queue_test", 5);
    }

    /**
     * 闭包方式实现lock锁定
     * @return mixed
     * @throws \Exception
     */
    public function callbackLock($data)
    {
        $count = 3;
        while ($count) {
            $count--;

            try{
                return $this->locked(function () use ($data){
                    return array_values($data);
                }, "lock_test_dd", 10);
            }catch (\Exception $e) {
                if($e->getCode() === -1) {
                    //lock fail retry
                    continue;
                }

                throw $e;
            }
        }

    }
}
```

### Redis配置

```php
//Redis缓存设置
return array(
    'default' => ['host' => '127.0.0.1', 'port' => 6379, 'pre' => 'ts_', 'pwd' => '123'],
    'fend_test' => ['host' => '127.0.0.1', 'port' => 6379, 'pre' => 'f_', 'pwd' => '', 'db'=> 1],
    'fend_test_broken' => ['host' => '227.0.0.1', 'port' => 6379, 'pre' => 'f_', 'pwd' => ''],
    'fend_test_pwd' => ['host' => '127.0.0.1', 'port' => 6379, 'pre' => 'f_', 'pwd' => '123']
);
```

### 其他的一些小细节

* redis配置目前放在/app/Config/Redis.php内
* 目前只是实现了简单的单例工厂模式，不同的配置请求过后都会有个实例一直保持长连接
* 断线重链
* 支持key前缀功能，可以在配置内设置

## 环境依赖

请使用新版本phpredis扩展：[https://github.com/phpredis/phpredis/releases](https://github.com/phpredis/phpredis/releases) V3.1.5 以上版本

老版本有小概率情况下连接成功但持续报错情况。

