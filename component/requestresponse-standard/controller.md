# Controller 说明

## 使用注意事项
 * Fend框架推荐将Controller都放置在app/Http下 
 * 如存在多个子站可以放置在其下不同目录，Fend路由支持多个域名指定到不同子目录 
 * PSR4标准要求类及namespace大小写和文件路径必须完全匹配，而网址URI是不区分大小写，所以规定app/Http目录下所有类及目录必须是首字母大写，其他部分为小写
 * Controller内所有对外公开的接口请定义为 public 而内部使用的函数必须定义为 private 以防止泄漏导致安全问题
 * Controller内函数被调用时，产生的任何`echo`及`输出屏幕`数据都不会在返回结果内呈现、只有return内容方可显示。（解决文件bom或echo导致header不能正常设置问题）
 * Controller内函数被调用时，产生的输出可以通过网址参数打开wxdebug=1模式查看，此功能可以配置线上不输出
 * 最终对用户返回内容建议为字符串，其他类型不推荐
 * 用户业务异常建议自行拦截，如不拦截Fend会提示500错误，并且返回白页（放置错误信息泄漏）

## 使用样例

```php
<?php

namespace App\Http;

use App\Model\DemoDBNCModel;
use Fend\Coroutine\Coroutine;
use Fend\Di;
use Fend\Fend;
use Fend\Funcs\FendHttp;
use Fend\Read;
use Fend\Request;
use Fend\Response;

/**
 * Class Index
 * 首页
 * @author gary
 */
class Index extends Fend
{

    public function Init(Request $request, Response $response)
    {
        $request->setQueryString("yes", 1);
    }

    /**
     * 首页演示
     * 访问地址： http://www.fend1.com/index/index?wxdebug=1
     *
     * @param Request $request
     * @param Response $response
     * @return mixed
     * @throws \Exception
     */
    public function index(Request $request, Response $response)
    {
        $table = Di::factory()->getTable("share");
        if ($table) {
            $table->set("11", ["time" => time(), "data" => "123"]);
            var_dump($table->get("11"));
        }
        //var_dump($request->get());

        //获取$_GET参数
        $user_id = $request->get("user_id");

        //echo输出内容只会在wxdebug=1时显示在控制台，而不会输出给用户
        echo "this is debug output test";

        //写入一条日志
        //Logger::info("this is log test $user_id");

        //如果index没有指定request参数和response参数可以通过这个方式获取
        $response = Di::factory()->getResponse();

        //返回结果是json的话可以使用这个，会顺便帮忙设置header头
        //json如果有特殊参数，可以跟随在这个后面
        $data = $response->json([microtime(true)]);

        //返回一个header头，大小写自理
        $response->header("xxx", "xxx");

        //throw new \Exception("test",1231);

        //返回内容直接以字符串形式return
        return $data;
    }

    /**
     * 500错误演示
     * 如果请求querystring带wxdebug=1会看到更多信息
     * 访问地址： http://www.fend1.com/index/excep?wxdebug=1
     *
     * @param Request $request
     * @param Response $response
     * @throws \Exception
     */
    public function excep(Request $request, Response $response)
    {
        //get parameter by query
        $userid = $request->get("userid");
        $username = $request->get("username");

        //test exception
        //抛出用户所见会白屏，http code 500 ，通过网址增加querystring wxdebug=1可以看到详细信息
        throw new \Exception("test", 1231);
    }

    /**
     * SQL query 演示
     * 如果请求querystring带wxdebug=1会看到更多信息
     * 访问地址： http://www.fend1.com/index/sql?wxdebug=1
     *
     * @param Request $request
     * @param Response $response
     * @return mixed
     * @throws \Exception
     */
    public function sql(Request $request, Response $response)
    {
        //SQL查询测试，更多的可以在App\Test\Db看到
        $con = array(">" => ["id" => 0], "user_sex" => 1);
        $field = array("*");
        $start = 0;
        $limit = 20;

        ////////////////老方法实现查询
        $mod = Read::Factory("users", "default");
        $mod->setConditions($con);
        $mod->setField($field);
        $mod->setLimit($start, $limit);
        $sql = $mod->getSql();

        $q = $mod->query($sql);
        while ($rs = $mod->fetch($q)) {
            $result['list'][] = $rs;
        }
        $result['total'] = $mod->getSum();

        ////////////////链式调用展示
        $mod->setConditions($con)->setField($field)->setLimit($start, $limit)->query()->fetch_all();

        ////////////////链式调用展示
        $mod->where([["id", ">=", 0]])->setField($field)->setLimit($start, $limit)->query()->fetch_all();

        ////////////////直接使用read里面带的函数，并开启了prepare
        $mod->getListByCondition($con, $field, $start, $limit, "id desc", true);

        ////////////////直接使用read里面带的函数,where新条件设置方法，并开启了prepare
        $mod->getListByWhere([["id", ">=", 0]], $field, $start, $limit, "id desc", true);

        ////////////////model方式可以直接继承\Fend\App\DBModel 按表进行操作，可操作函数同Read|Write

        //设置返回cookie
        $response->cookie("kkdddd", "vvfdfdfd");

        $model = new DemoDBNCModel();
        $result1 = $model->getListByWhere([]);
        var_dump($result1);

        //$redis = Cache::factory(Cache::CACHE_TYPE_XESREDIS,"test");
        //var_dump("redisSdkTest", $redis->set("yes","11"));

        return $response->json($result);
    }


    public function status(Request $request, Response $response)
    {
        return  $response->json(\Swoole\Coroutine::stats());
    }

    public function test()
    {
        $time = microtime(true);
        $ret = FendHttp::getUrl("127.0.0.1/111");
        $ret = FendHttp::getUrl("127.0.0.1/111");
        $ret = FendHttp::getUrl("127.0.0.1/111");
        $ret = FendHttp::getUrl("127.0.0.1/");
        return bcsub(microtime(true), $time, 4);
    }

    private function getPid($id)
    {
        $pid = $id;
        while (1) {
            $cid = \Swoole\Coroutine::getPcid($pid);
            if ($cid === -1 || $cid === false) {
                return $pid;
            }
            $pid = $cid;
        }
    }

    public function go()
    {

        echo Coroutine::getPcid(), "\n";
        go(function () {
            echo Coroutine::getPcid(), "\n";
            go(function () {
                var_dump($this->getPid(\Swoole\Coroutine::getCid()), 1111);
                echo Coroutine::getPcid(), "\n";
                go(function () {
                    echo Coroutine::getPcid(), "\n";
                    var_dump($this->getPid(\Swoole\Coroutine::getCid()), 2222);

                    go(function () {
                        echo Coroutine::getPcid(), "\n";
                    });
                    go(function () {
                        echo Coroutine::getPcid(), "\n";
                    });
                    go(function () {
                        echo Coroutine::getPcid(), "\n";
                        var_dump($this->getPid(\Swoole\Coroutine::getCid()), 3333);

                    });
                });
                echo Coroutine::getPcid(), "\n";
            });
            echo Coroutine::getPcid(), "\n";
        });
        echo Coroutine::getPcid(), "\n";
    }


    public function UnInit(Request $request, Response $response, &$result)
    {
        //$result = $result . "\r\n Uninit append text";
    }
}

```
