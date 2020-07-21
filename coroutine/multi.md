# 并发调用

## 在请求中创建一个子协程

Fend提供了一个简单封装用于在请求中创建一个子协程：

```php
\Fend\Coroutine\Coroutine::create(function() {       
    // 在这里填写子协程的逻辑
});
```

在这里，子协程创建后，主协程会让出进程，由子协程运行，当子协程遇到IO阻塞或协程sleep后，会再次让出进程，由主协程运行。 

`注意：请使用框架提供的创建协程封装，不要使用原生Swoole的go或swoole\coroutine::create否则上下文会出现混乱`

下面是一个运行示例：

```php
\Fend\Coroutine\Coroutine::create(function () {
    echo "1\n";
    \Fend\Coroutine\Coroutine::create(function () {
        echo "1.1\n";
        \Fend\Coroutine\Coroutine::sleep(1);
        echo "1.2\n";
    });
    echo "2\n";
    \Fend\Coroutine\Coroutine::sleep(1);
    echo "3\n";
});
```

这段代码的输出为：

```bash
$ php coroutine.php
1
1.1
2
1.2
3
```

## 协程并发调用

过去，如果我们需要发起十个SQL请求，我们的代码运行方式是这样的：

* 发起请求1
* 接受请求1
* 发起请求2
* 接受请求2
* ... 
* 发起请求10
* 接受请求10

这样，我们的耗时等于 `SUM(time1, time2 ... , time10)`

而在协程模式下，我们可以实现这样的模式：

* 发起请求1
* 发起请求2
* ... 
* 发起请求10
* 接受请求1
* 接受请求2
* ... 
* 接受请求10

这样，我们的耗时等于 `MAX(time1, time2 ... , time10)`

(当然实际情况下，是达不到这么好的效果的，但是总的耗时是远远低于串行调用的)

在Fend框架里，可以通过这样的方式来实现调用：

```php
$result = \Fend\Coroutine\Coroutine::multi([
    'key1' => function() {
        // 请求1
        return 1;
    }，
    'key2' => function() {
        // 请求2
        return 2;
    }，
    'key3' => function() {
        // 请求3
        return 3;
    }，
]);
```

得到的 `$result` 结果为：

```php
[ 
    'key1' => 1, 
    'key2' => 2, 
    'key3' => 3, 
]

```