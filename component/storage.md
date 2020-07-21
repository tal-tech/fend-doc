# 数据存储驱动组件
fend-plugin-db组件，提供对SQL关系数据库驱动统一创建管理，内置SQLBuilder拼装，读写分离封装，函数映射数据操作封装，Model，Model自动数据缓存

目前可选驱动：
 * Mysqli驱动 (fend-plugin-mysql)
 * MysqlPDO驱动 (fend-plugin-pdo)

## SQL拼装器的使用

SQLBuilder：\Fend\Read 及 \Fend\Write继承SQLBuilder类，并注入Mysqli或MysqlPDO驱动到私有变量

提供SQL拼装(函数方式、链式调用方式、prepare + 人工拼SQL方式)，where条件翻译，SQL安全函数检测，Prepare参数自动过滤，链路跟踪

## 数据操做使用时可选集中方式：
> * 直接用\Fend\Read 或 \Fend\Write factory返回的对象，对数据使用函数进行操作如getInfoById，支持自动prepare，支持参数过滤
> * 直接用\Fend\Read 或 \Fend\Write factory返回的对象，链式调用
> * 继承DBModel或DBNCModel 后$this->getInfoById(1);