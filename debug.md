# 开发调试

## 框架调试模式
正常业务代码产生错误及异常的时候是不会输出到返回内容（只会记录日志） 

开启后在请求网址内增加wxdebug=1参数即可看到调试界面 
 
配置路径: \app\Config\Fend.php 

配置选项: "debug" => true 

如图： 

![debug](assets/wxdebug_main.png)

 * 输出所有warning及notice错误提示
 * 黑框是接口输出内容，如果是json那么自动会美化
 * 黑框右上角是当前框架运行模式fpm/swoole_v1 非协程模式/swoole_v4 协程模式/cli
 * Console是代码在执行期间echo的内容
 * Exception 是运行过程中产生的异常信息
 * SQL是代码执行过程产生的SQL及对应参数、耗时
 * File是框架目前加载到内存的文件及大小
 * Env为系统环境变量，内含cookie、server、GET、POST信息
 
### wxdebug参数选项
 
  * 网址内参数加wxdebug=1可以查看debug模式，本地性能分析，具体如下：
   > * 0：关闭
   > * 1：开启wxdebug模式 + html界面
   > * 2：开启wxdebug模式 + var_dump输出所有信息
   > * 3：不开启界面 + 生成xhprof Profile

注意：如果只是显示html界面不切换，请升级fend-core组件，并且替换fend-demo内www\index.php到您的项目中

### Xhprof本地性能分析 
使用之前需要安装 xhprof组件:[https://github.com/midoks/md_xhprof](https://github.com/midoks/md_xhprof)  
并设置如下配置到php.ini
```ini
[xhprof]
extension = md_xhprof.so
xhprof.output_dir = /tmp/xhprof 
```
请求接口 + 参数&wxdebug=3 会生成Xhprof性能分析文件到xhprof.output_dir 

<font color=red>注意：由于这个扩展是第三方维护的，不能保证是否有漏洞或故障，请一定在本地测试使用，线上不要使用！切记！切记！</font>

## 线上调试
请参考 [分布式链路跟踪章节](component/trace.md) 

