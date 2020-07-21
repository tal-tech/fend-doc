# 数据过滤组件

## 引入
当前项目默认集成到fend-core内

## XSS过滤、Html简化
需要引入组件
```bash
composer require ezyang/htmlpurifier 4.12
```

然后使用如下即可过滤

```php
use Fend\Filter as Filter
$html = file_get_content("http://www.baidu.com"); //仅供展示，生产别用这个函数代替curl
$filteredHtml = Filter::purifierHtml($html);
```

## 其他过滤

```php
use Fend\Filter as Filter

/**
 * 过滤字符串,特殊字符转义，规范配对tag
 * @param string $string 输入string
 * @param int $flag 附加选项flat
 * @return mixed 过滤结果
 */
$result = Filter::filterString($string, $flag = null);

/**
 * 过滤特殊字符串，不可见字符，html标志<>&
 * @param string $string 输入string
 * @param int $flag 附加选项flat
 * @return mixed 过滤结果
 */
$result = Filter::filterSpecialChars($string, $flag = null);

/**
 * 使用Sanitize过滤网址内非法字符串
 * @param string $url
 * @return mixed
 */
$result = Filter::filterUrl($url);

/**
 * 过滤email地址中非法字符
 * @param string $email
 * @return mixed
 */
$result = Filter::filterEmail($email) ;

/**
 * 过滤字符串中float非数值部分，不包括小数点 加减号 科学技术法标志(E)
 * @param string $float
 * @return mixed
 */
$result = Filter::filterFloat($float) ;


/**
 * 过滤int，包括小数点 加减号
 * @param string $float
 * @return mixed
 */
$result = Filter::filterInt($int) ;
```