# MysqlI

注意：Mysqli驱动和PDO驱动有一定细节差异，如Mysqli返回数值是字符串类型

### PHP扩展依赖
 * mysqli
 * mysqlnd


## 如何使用Mysqli驱动

### Read|Write 切换
```php
//Read、Write类初始化时，传递第三个参数, 默认是Mysqli驱动
$db = \Fend\Write::Factory("xes_user_info_table","xes_user_center_db", "Mysql");
```

### DBModel 切换
```php
class DemoDbModel extends DBModel
{
    protected $_driver = 'Mysql';
}
```