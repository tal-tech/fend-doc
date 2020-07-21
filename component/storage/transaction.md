# 事务

### 闭包 驱动级(Mysql.php|MysqlPDO)
```php
//author by xialei
$mysql = Write::Factory($this->_table, $this->_db, "Mysql");
$result = $mysql->transaction(function($mysql){
  // 处理事务
  //失败会自动回滚
  return true;
});

```
### 闭包 驱动级(Mysql.php|MysqlPDO)-老方式
```php
$mysql = Write::Factory($this->_table, $this->_db, "Mysql");
if($db->trans_begin()){
	try{
		//do something
		$db->trans_commit();        
	}catch(\Exception $e){
		$db->trans_rollback();        
	}
}
```

### 非闭包 用户自封Model调用方式
```php
class Model_User extends Fend
{

    private $_table     = 'ucm_sysuser';  //表名
    private $_db        =  'ht';          //数据库配置名称

    //单例
    public static function factory()
    {
        return new self();
    }
    
    public function start()
    {
        $db= \Fend\Write::Factory($this->_table,$this->_db);
        if($db->trans_begin()){
            try{
                //do something
                $db->trans_commit();        
            }catch(\Exception $e){
                $db->trans_rollback();        
            }
        }
    }
}
```

### 非闭包 \Fend\App\DBModel继承类
```php
    if($this->writeModel->trans_begin()){
        try{
            //do something
            $this->writeModel->trans_commit();        
        }catch(\Exception $e){
            $this->writeModel->trans_rollback();        
        }
    }
    
```
### 闭包 \Fend\App\DBModel继承类方式
```php
$result = $this->transaction(function($mysql){
  // 处理事务
  // 抛出异常会自动回滚
});
```

### 其他注意事项
 * 使用事务时，读写都需要从写库执行，读库获取不到刚插入数据
 * 使用事务时，有cache操作需慎重，事务回滚会导致cache保存错误的数据