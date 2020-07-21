# Composer常见问题

## 设置全局composer
建议使用aliyun composer mirror及网校内部packagist，注意命令执行顺序
```bash
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

### 如何获取composer错误信息
在执行composer命令时增加-vvv参数，可以看到composer更详细的信息输出

## composer使用说明

### 开发环境
使用如下composer命令拉取composer组件，会将composer.json require-dev内组件拉取，方便本地单元测试、调试
```bash
composer install 
```

### 线上环境
使用如下composer命令拉取composer组件，不会拉取composer.json中require-dev部分，性能会有提升
```bash
composer install --no-dev 
```

### 查看已安装composer组件版本
```bash
composer info
```

### 组件更新及版本锁定

第一次composer install时如果使用如上版本设置，会自动拉取对应大版本号最新版本组件如:1.2.32或1.3.33，版本信息会存储在composer.lock内 

删除vendor后再次执行composer install时，composer会按照composer.lock内版本信息及对应packigst再次拉取，（如果出现tag版本删除重建情况这里会报错，除非删除composer.lock） 

如需升级对应组件可临时更改composer.json对应版本后执行composer update

另外，如果不想使用composer.lock、可以使用composer info查看当前组件版本并同步更新到composer.json内即可锁定版本 

锁定版本好处在于: 新组件如发生不兼容情况、会导致发版时动态拉取composer导致线上故障，建议线上用户慎重使用\~1.2.0类似版本定义
