# PDO 驱动

使用PDO驱动，会更安全，所有对外接口和Mysqli一致

注意：Mysqli驱动和PDO驱动有一定细节差异，如Mysqli返回数值是字符串类型，PDO会返回数值类型

### PHP扩展依赖
 * pdo
 * mysqlnd
 * pdo_mysql


## 如何切换成PDO Mysql

### Read|Write 切换
```php
//Read、Write类初始化时，传递第三个参数
$db = \Fend\Write::Factory("xes_user_info_table","xes_user_center_db", "MysqlPDO");
```

### DBModel 切换
```php
class DemoDbModel extends DBModel
{
    protected $_driver = 'MysqlPDO';
}
```
