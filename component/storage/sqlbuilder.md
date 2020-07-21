# SQL Builder

`SQLBuilder`位于`\Fend\Db\SQLBuilder.php`，提供SQL拼装、Prepare查询 
 
SQLBuilder 实现由 `\Fend\Read` 查询数据（读库），`\Fend\Write` 增删改查（写库）,请使用Read|Write操作数据库。

## 强制规则

* 表自增主键字段名必须为id


## 驱动代码结构组成及示范
```php
$db = \Fend\[Write|Read]::Factory(string $db,string $configKey, string $driver); 
$db           "数据库表名" 
$configKey    "DB配置对应的key，默认Default" 
$driver       "数据驱动：Mysql(默认)|MysqlPDO" 
```

通过如下代码可以获取SQLBuilder对象，目前Fend对于依赖数据都是使用工厂模式获取

```php
//写操作使用Write获取
$db = \Fend\Write::Factory("xes_user_info_table","xes_user_center_db");

//读操作使用Read获取
$db = \Fend\Read::Factory("xes_user_info_table","xes_user_center_db");
```

### 原生SQL执行及Prepare
Fend原设计目标是不希望用户使用ORM去做`复杂SQL查询`，因为`复杂查询的索引是否有效`是不可控

如果有复杂的SQL，`请先写出原生SQL提交给DBA审核`、然后使用如下方法执行 

另外query方法提供了`Prepare`可选参数、建议使用

SQL查询
```php
$sql ="select * from users where id in (1,2,3,4)";
$result = $db->query($sql);
$result = $db->fetch($result);
```

Prepare Query
```php
$sql ="select * from users where id in (?,?,?,?)";
$bindparam = [1,2,3,4];
$result = $db->query($sql,$bindparam);
$result = $db->fetch($result);
```

### Read|Write 内置操作函数
为了提高工作效率，Read及Write内置了一些常见的增删改查操作 

大部分内置函数最后一个参数prepare设置为true即可开启`prepare` 

下面是一些使用例子：
```php
//获取原生驱动mysql对象
//用于特殊需要用途，不推荐
$mod = Read::Factory($this->_table, $this->_db);;

//添加数据，成功返回insertid
$data = array(
   "username" => "test_user_name",
   "sex" => 1,
);
$inserId = $db->add($data);
//prepare开启、添加数据，成功返回insertid
$inserId = $db->add($data, true);

$multiData = array(
   "username" => "test_user_name",
   "sex" => 1,
);
//批量添加数据,成功返回true
$result = $db->addMulti($muliData, true);

//更新符合条件的数据
//错误返回数组内含原因，成功返回true
$ret = $db->edit($where, $data);

//更新指定主键id的数据
//错误返回数组内含原因，成功返回true
$ret = $db->editById(4375, $data);

//根据where条件批量更新数据
//注意函数以ByWhere结尾的函数，条件使用新where语法，关于where语法后续会详细介绍
$ret = $db->editByWhere([["id", ">=", 1]]);

//删除符合条件的数据
$ret = $db->del($where);
$ret = $db->delByWhere([["username" => "test_user_name"], ["sex" => 1] ]);

//根据主键id获取一条数据，并且只显示指定字段，如果不指定默认全返回
$info = $db->getById(4379, array("id","username","status"));

//获取数组内指定id列表数据，并且只显示指定字段，如果不指定默认全返回
$list = $db->getByIdArray(array(432,3432,1223), array("id","username","status"));

//根据条件返回全量数据,这个没有limit要小心
$list = $db->getListByCondition($where, array("id","username","status"), "id desc");
$list = $db->getListByWhere([["username" , "test_user_name"], ["sex" , ">=", 1] ], array("id","username","status"), "id desc");

//根据条件获取一条数据，如果返回多条，拿第一条
$info = $db->getInfoByCondition($where, array("id","username"));
$info = $db->getInfoByWhere([["username" , "test_user_name"], ["sex" , 1] ], array("id","username"));

//根据条件，翻页获取数据，如果数据量巨大，建议优化一下，否则翻页越翻越慢
$list = $db->getDataList($where, 0, 100, array("id","username"))
$list = $db->getDataListByWhere([["username" , "test_user_name"], ["sex" , 1] ], 0, 100, array("id","username"))

//count(*) 以及互斥锁(for update)
$count = $db->lock()->getCount($where);

//count(*) by where 以及共享锁(LOCK IN SHARE MODE)
$count = $db->shareLock()->getCountByWhere([["username" , "test_user_name"], ["sex" , 1] ]);

//left join
$mod = Read::Factory($this->_table, $this->_db);
$result = $mod->getLeftJoinListByWhere("user_info", ["id" => "user_id"], [["users.id", ">", 0]], "users.id, users.account, users.user_name, user_info.score, user_info.gold", 0, 20, "", true);

//left join count
$mod = Read::Factory($this->_table, $this->_db);
$result = $mod->getLeftJoinCountByWhere("user_info", ["id" => "user_id"], [["users.id", ">", 0]], true);

//left join group having
$result = $mod->getLeftJoinGroupHavingListByWhere(
            "users.id, users.account, users.user_name, user_info.score, user_info.gold", //field
            "user_info",                //right table
            ["id" => "user_id"],        //on 
            [["users.id", ">", 0]],     //where
            "account",                  //group
            [["users.id", ">", "3"]]    //having
        );
```

### 使用SQLBuilder生成SQL
```php
//获取一个insert sql文本
$data = array(
   "username" => "test_user_name",
   "sex" => 1,
);
$sql = $db->addForSql($data);

//获取一个update的sql文本
$sql = $db->editByIdForSql($inserId, $data);

//老condition条件where生成，目前只支持两种写法
$condition = "`id` = 4375 and `status` = 1"; //第一种为字符串
$condition = array("id"=>4375,"status" => 1);//第二种写法，多个条件都为and方式
$condition = array(">="=> array());//第二种写法，多个条件都为and方式

//获取根据条件更新的sql文本
$sql = $db->editForSql($where, $data);

//subSQL用于增删改SQL生成
//获取update SQL
$sql = $mod->subSQL($data,$this->_table,'update', [],[]);

//生成create_time = create_time + 1; 更新SQL
$sql = $mod->subSQL(["create_time" => ["+", 1]],$this->_table,'update', [],[]);

//生成Condition条件语法查询SQL
$mod = Read::Factory("users", "default");
$mod->setConditions(["user_sex"=>1,">=" => ["id", 0]]);
$mod->setField($field);
$mod->setLimit($start, $limit);
$sql = $mod->getSql();

//生成where条件语法查询SQL
$mod = Read::Factory("users", "default");
$mod->where([["username" , "test_user_name"], ["sex" , 1] ]);
$mod->setField($field);
$mod->page(1, $limit);
$sql = $mod->getSql();
```

### SQLBuilder拼SQL并执行
```php
//SQL查询测试，更多的可以在App\Test\Db看到
$con   = array(">" => ["id" => 0], "user_sex" => 1);
$field = array("*");
$start = 0;
$limit = 20;

////////////////老方法实现查询
$mod = Read::Factory("users", "default");

//getSQL用于查询SQL生成
$mod->setConditions($con);
$mod->setField($field);
$mod->setLimit($start, $limit);
$sql = $mod->getSql(); 

$q = $mod->query($sql);
while ($rs = $mod->fetch($q)) {
    $result['list'][] = $rs;
}
$result['total'] = $mod->getSum();

//清理上次操作残留信息，或使用Read::Factory("users", "default");再次生成新SQLBuilder对象
$mod->clean();

//subSql用于插入修改SQL生成，如
$mod->subSQL($rs, $this->_table, 'insert');
$mod->query($sql);
$id = $mod->getLastId();

//一个查询演示使用where条件语法
$mod = Read::Factory("users", "default");
$mod->where([["user_sex", "in", [1,2]]]);
$mod->setField($field);
$mod->setLimit($start, $limit);
$result = $mod->getList();

`注意`：一次Factory的对象建议只操作一次SQL，如果用于新查询，建议使用$mod->clean()清理下上次查询残留（只有链式调用需要、Read、Write及Model已经实现了清理）
```

### SQLBuilder支持链式操作
```php
$mod = Read::Factory("users", "default");

////////////////链式调用展示
$mod->setConditions($con)->setField($field)->setLimit($start, $limit)->getList();

////////////////链式调用展示
$mod->where([["id", ">=", 0]])->setField($field)->setLimit($start, $limit)->getList();

////////////////直接使用read里面带的函数，并开启了prepare
$mod->getListByCondition($con, $field, $start, $limit, "id desc", true);

////////////////直接使用read里面带的函数,where新条件设置方法，并开启了prepare
$mod->getListByWhere([["id", ">=", 0]], $field, $start, $limit, "id desc", true);

//同一个mod对象，每次进行新查询时，需要重置
$mod->clean();

```

## Prepare
所有函数方式都支持prepare操作，会自动将数组传入的kv数据及条件自动进行加工检测 
 
Fend\Read|Write开启方法，函数最后一个参数 

## Condition条件规则
condition是老方式where条件生成
```php
$mod = Read::Factory($this->_table, $this->_db);
$conditions = [
    '>=' => [
        'created_at' => '2019-09-02'
    ]
];

try{
    $list = $mod->getListByCondition($conditions);
}catch(\Exception $e) {

}
self::assertEquals($mod->getLastSQL()["sql"],"SELECT * FROM users WHERE  created_at >= '2019-09-02'  LIMIT 0,20");

$conditions = [
    "id" => "03883231207814667379197355882525",
];
try{
    $list = $mod->getListByCondition($conditions);
}catch(\Exception $e) {

}
self::assertEquals($mod->getLastSQL()["sql"],"SELECT * FROM users WHERE  `id` = '03883231207814667379197355882525' LIMIT 0,20");
```

## Where条件规则
where规则支持多种方式，具体看演示
```php
$mod = Read::Factory($this->_table, $this->_db);

$where = [
    "(", //多层嵌套可以人工设置括号

    "(",
    ['fend_test.`db`.user_id', 14],
    ['users.user_name', 'oak'],
    ['`users`.user_id', ">=", 0],
    ")",

    "OR",

    "(",
    ['`user_id`', "<=", 10000],
    ['user_id', "like", '57%'],
    ")",

    ")", //支持多层嵌套

    "OR",

    ['user_id', "in", [1, 2, 3, 4, 5, 6]],

    "OR",//人工设置条件关系 OR

    "(",
    ['user_id', "not in", ['a', 'c', 'd', 'f']], //三元第三参数支持数组
    " `user_name` = 'yes' ", //自定义对比 可以直接用一行字符串
    ")",
];
$mod->where($where);
$sql = $mod->getSql();
self::assertEquals('SELECT  *  FROM users WHERE   (  (  `fend_test`.`db`.`user_id` = 14 AND `users`.`user_name` = \'oak\' AND `users`.`user_id` >= 0 )  OR   (  `user_id` <= 10000 AND `user_id` like \'57%\' )  )  OR   `user_id` in (1,2,3,4,5,6) OR   (  `user_id` not in (\'a\',\'c\',\'d\',\'f\') AND  `user_name` = \'yes\'  ) ', $sql);
```

## 关于上次SQL操作残余问题

最近review代码时，发现Read|Write 工厂模式出来的mod对象，如果操作特殊操作(group join)会有条件残留问题 

碰到这类问题有两个方式可以解决，再次使用factory工厂出一个新的对象或执行$mod->clean();


## 安全过滤

目前底层对于sql做了安全检测，如果出现sql中有注释，非法函数调用，字符串截取等操作会被拒绝，具体如下：

 * load\_file
 * hex
 * substring
 * if
 * ord
 * char
 * intooutfile
 * intodumpfile
 * unionselect
 * \(select
 * unionall
 * uniondistinct
 * @
