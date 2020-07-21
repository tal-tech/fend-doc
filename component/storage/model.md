# Model

## Model 人工封装样例

此演示用于展示如何人工封装一个简易model

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

    /**
     * 获取用户列表
     * @param  array     $con    条件
     * @param array $field  字段
     * @param int   $start  开始
     * @param int   $limit  总条数
     * @return array
     */
    public function getList($con=array(),$field=array(),$start=0,$limit=20)
    {
        $tmy    = array('list'=>array(),'total'=>0);
        $mod   = \Fend\Read::Factory($this->_table,$this->_db); //使用读库配置
        $mod->setConditions($con); //设置where条件
        $mod->setField($field); //设置输出数据字段
        $mod->setLimit($start,$limit); //offset及limit
        $sql = $mod->getSql(); //生成sql
        $q   = $mod->query($sql); //执行查询
        while($rs = $mod->fetch($q)){ //多行获取结果

            $tmy['list'][] = $rs;
        }
        $tmy['total'] = $mod->getSum(); //根据where条件生成sum sql语句并执行，获取数据总数
        return $tmy;
    }
    /**
     * 获取用户信息
     * @param   array    $con    条件
     * @param array $field  字段
     * @param int   $start  开始
     * @param int   $limit  总条数
     * @return array
     */
    public function getInfoByCondition($con=array(),$field=array(),$start=0,$limit=20)
    {
        $tmy    = array('list'=>array(),'total'=>0);
        $mod   = \Fend\Read::Factory($this->_table);
        $mod->setConditions($con);
        $mod->setField($field);
        return $mod->getOne(); //获取一条结果
    }

    /**
     * 添加&修改
     *
     * @param array $item 添加话题信息
     * @return int 栏目
     **/
    public function add($item)
    {
        //此处为输入过滤，指定字段方可入库，并且如果是数值类型，会强制转换类型
        $rs=array(
            'id'        =>0,//信息ID
            'name'      =>'',//账户
            'email'     =>'',//邮箱
            'nickname'  =>'',//昵称
            'pwd'       =>'',//密码
            'tpower'    =>'',//权限
            'phone'     =>0,//手机号
            'gid'       =>'',//分组
        );

        //连接DB
        $db= \Fend\Write::Factory($this->_table,$this->_db);  //写操作使用write获取db对象

        //过滤，转化类型，赋值集合数据
        foreach($rs as $k=>$v){
            if(isset($item[$k])){
                $rs[$k]=is_numeric($v) ? (int)$item[$k] : $db->escape(stripslashes($item[$k]));
            }else{
                unset($rs[$k]);
            }
        }
        //我很努力的发掘，但还是为找到符合表的字段
        if(count($rs)<=0) return 0;
        //用户传递了主键，那么直接更新
        if(!empty($rs['id'])){
            $rs['ctime'] = time();
            $sql = $db->subSQL($rs,$this->_table,'insert');
            $db->query($sql);
            $rs['id'] = $db->getLastId(); //数据插入成功id
        }else{
            //没有传递主键那么插入新数据
            $rs['utime'] = time();
            $sql = $db->subSQL($rs,$this->_table,'update'," id={$rs['id']}");
            $db->query($sql);
        }
        return $rs;
    }

}
```

## 继承DBModel
继承DBModel类可以使用带Cache的数据功能 

如果某mysql表统一使用此类对其进行增删改查操作  

那么能够实现准实时数据cache服务

适合修改少，查询多数据表  

cache原理为：
 > * 单条数据cache：增删改会对此表的单条数据更新同步推送到cache内，cache存在直接从cache返回用户
 > * 条件查询cache：增删改会更新表对应的版本计数器，下次访问时，从cache走的where查询数据，如发现版本号不相同走Mysql，并更新cache

注意：如不需要cache功能，建议继承DBNCModel，查询效率更高。

DBModel封装了常见的增删改查，字段过滤功能，自动读写分离

```php
namespace App\Model;

use Fend\App\DBModel;
use Fend\Cache;

class DemoDbModel extends DBModel
{

    //string 数据库表名, 继承根据需要覆盖
    protected $_table = 'users';

    // string 数据库配置名称, 继承根据需要覆盖
    protected $_db = 'default';

    //cache key 前缀, 继承根据需要覆盖
    protected $_cachePrefix = "tb";

    //bool $_preapare 是否开启prepare查询
    protected $_prepare = true;

    //事务期间自动切换使用写库进行查询
    //注意，此功能会导致每次新建model操作链接主库
    protected $_autoRWSwitch = true;

    /////////////////数据cache

    //是否开启单条数据cache 继承根据需要覆盖
    protected $_cacheSingleData = true;

    //是否开启where数据Cache 继承根据需要覆盖
    protected $_cacheSearchData = true;

    //默认cache时间, 继承根据需要覆盖
    protected $_cacheExpire = 60;

    /**
     * DemoDbModel constructor.
     * @throws \Exception
     */
    public function __construct()
    {
        parent::__construct();

        $this->setFieldList([
            "account"     => "string",
            "passwd"      => "string",
            "user_sex"    => "int",
            "user_name"   => "string",
            "create_time" => "int",
            "update_time" => "int"
        ]);

        //注入cache对象，用于保存cache
        $this->_cache = Cache::factory(Cache::CACHE_TYPE_REDIS, "fend_test");
    }

}
```
## 继承DBNCModel
方式同DBModel，只不过少了cache相关定义字段

```php

namespace Test\App;

use Fend\App\DBNCModel;
use Fend\Cache;

class DemoDBNCModel extends DBNCModel
{

    //string 数据库表名, 继承根据需要覆盖
    protected $_table = 'users';

    // string 数据库配置名称, 继承根据需要覆盖
    protected $_db = 'default';


    //bool $_preapare 是否开启prepare查询
    protected $_prepare = true;

    //事务期间自动切换使用写库进行查询
    protected $_autoRWSwitch = true;

    /**
     * DemoDbModel constructor.
     * @throws \Exception
     */
    public function __construct()
    {
        parent::__construct();

        $this->setFieldList([
            "account"     => "string",
            "passwd"      => "string",
            "user_sex"    => "int",
            "user_name"   => "string",
            "create_time" => "int",
            "update_time" => "int"
        ]);

        //注入cache对象，用于保存cache
        $this->_cache = Cache::factory(Cache::CACHE_TYPE_REDIS, "fend_test");
    }

    public function addUser()
    {
        $data = [
            "account"     => "test",
            "passwd"      => "123",
            "user_sex"    => 1,
            "user_name"   => "xcl",
            "create_time" => time(),
            "update_time" => time()
        ];
        
        //强迫所有操作使用mysql主库
        $this->forceWrite(true);

        $this->transactionCallback(function () use ($data){
            $id = $this->add($data);
            $info = $this->getInfoById($id);

            if(!$info) {
                throw new \Exception("add fail",123);
            }

            if(!$this->delById($id)) {
                throw new \Exception("del fail",124);
            }
        });
        $this->forceWrite(false);
    }

}
```
## DBNCModel及DBModel功能函数
|函数|功能|备注|
|:----|:-----|:--------|
|factory|生产一个单例实例，会自动new继承类 | 使用此方式创建类为单例，注意|
|setFieldList|设置model数据类型及过滤字段列表|用于数据过滤，类型转换 |
|filterFieldData|按上一个函数设置过滤数据|unset不白名单字段，类型转换||
|forceWrite|强制查询操作使用主库 | 适用于特殊需要 |
|getModel| 获取底层Read\Write对象 | 用于特殊查询拼装 |
|add|添加一条数据 | 数据会做字段类型、白名单检测、并提供prepare功能 |
|addMulti|批量添加数据，会返回成功添加个数| 一条插入失败会全失败|
|updateByCondition| 根据condition条件语法更新数据 | 如果没有条件返回false|
|updateByWhere| 根据where条件语法更新数据 | 如没有设置条件返回false|
|updateById| 更新指定id数据 | 如没有id会拒绝执行|
|delByCondition| 根据condition条件语法删除数据 | 如没有条件返回false|
|delByWhere|  根据where条件语法删除数据 | 如没有条件会返回false|
|delById| 删除指定id数据 | 如没有id会拒绝执行|
|getInfoById|  获取指定id数据 | 如没有id会拒绝执行 |
|getInfoByIdArray| 更新指定多个id数据 | 如没有id返回false|
|getListByCondition| 根据condition条件语法获取数据列表 | 默认有limit 20|
|getListByWhere| 根据where条件语法获取数据列表 | 默认有limit 20|
|getInfoByCondition| 根据condition条件语法获取一条数据 | |
|getInfoByWhere| 根据where条件语法获取一条数据 | |
|getListPageByCondition| 根据condition条件语法获取指定条数数据 | 同时会返回整体符合条件数据个数 |
|getListPageByWhere| 根据where条件语法获取指定条数数据 | 同时会返回整体符合条件数据个数 |
|getCount| 根据condition条件语法获取符合条件数据个数 | |
|getCountByWhere|根据where条件语法获取符合条件数据个数 | |
|getSumByGroup|根据condition条件语法group by查询数据 | 默认支持count，也可以自行定制field|
|getSumByGroupList|根据where条件语法获取符合条件数据个数 | 默认为count，可以自行定制field|
|getLastSQL| 获取最后一次请求SQL语句| |
|getLastInsertId|获取最后一次操作数据id| |
|getAffectRow|获取最后一次操作影线数据个数|
|transaction|开启事务，重复开启不会报错|事务期间如果开启 autoRWSwitch 属性，查询操作都会在主库|
|commit|提交事务 不在事务中会报错| 事务期间如果开启 autoRWSwitch 属性，查询操作都会在主库|
|rollBack| 回滚事务 不在事务中不会报错|事务期间如果开启 autoRWSwitch 属性，查询操作都会在主库|
|openPrepare| 开启关闭查询自动prepare功能|开启后可以预防注入|
|transactionCallback| 通过闭包方式开启事务，闭包执行结束后自动提交，中途产生异常自动回滚| |
|getTableName| 获取当前model对应的表名 | |

## 事务
使用DBModel或DBNCModel时、开启事务请谨记
 * 开启$mode->forceWrite(true);，否则刚插入的数据无法读取到
 * DBModel所有操作必须关闭cache，否则回滚后会导致数据脏读cache
 * 同一个mysql链接在底层同一个协程下是单例，所以new多个DBModel及DBNCModel，都会在开启事务范围内

演示代码如下： 

```php
//测试多个DBModel事务回滚
$data = [
    "account" => "xcl@test.com",
    "passwd" => "user_test_pwd",
    "user_sex" => 1,
    "user_name" => "test1",
    "create_time" => time(),
    "update_time" => time(),
];

$model1 = new DemoDbModel();
$model2 = new DemoDbModel();
$model1->forceWrite(true);
$model2->forceWrite(true);

$model1->transaction();

$id1 = $model1->add($data);
$id2 = $model2->add($data);

self::assertNotEmpty($id1);
self::assertNotEmpty($id2);

$info1 = $model1->getInfoById($id1, [], false);
$info2 = $model1->getInfoById($id2, [], false);

self::assertNotEmpty($info1);
self::assertNotEmpty($info2);

$model2->rollBack();

$info1 = $model1->getInfoById($id1, [], false);
$info2 = $model1->getInfoById($id2, [], false);

self::assertEmpty($info1);
self::assertEmpty($info2);
```
