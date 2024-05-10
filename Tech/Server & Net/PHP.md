### 杂项

#### 变量

格式：$variable_name

* 必须以字母或者下划线开始；
* 只能包含字母，数字，下划线，不能包含空格；
* 区分大小写；

变量类型：local/global/static/parameter

和C语言不同的是，C语言全局变量可以被任意函数在文件任意位置调用；而php中想做到这一点需要使用global关键字；默认情况下一个函数只能调用并回显在函数体内部被定义的变量。

global关键字有如下使用方法（当一个变量在函数外部被声明后）

```
global $x
OR
$GLOBALS['x']
```

PHP 将所有全局变量存储在一个名为 $GLOBALS[*index*] 的数组中。 *index* 保存变量的名称。这个数组可以在函数内部访问，也可以直接用来更新全局变量。

在C语言中一个函数内被定义的变量在函数运行完成后会被销毁；在php中可以通过在第一次声明时添加static关键字使其成为静态变量。（虽然值不会被销毁但它仍然是函数的局部变量）

parameter：在函数内部通过传参的方式定义的局部变量

#### 输出语句

print和echo都可以不加括号，当然加也是可以的，，，

区别：echo可以一次输出多个字符串，使用逗号分隔；print只能输出一个字符串。

echo没有返回值，print有返回值1。这说明了echo本质上并不是函数，而是一种结构。

#### EOF

开始的符号为<<<EOF，结束符号为EOF。结束的EOF必须另起一行并在结尾加分号。之间的内容会被当成字符串输出，且单引号、双引号等会原样输出。其中变量可以被解析但函数不可以。

参考文档：https://newsn.net/say/php-eof.html



#### 变量类型

1.字符串：PHP中对字符串的语法要求比较宽松，只需要用单引号或者双引号括起来即可；

2.整型：

整数必须至少有一个数字 (0-9)；
整数不能包含逗号或空格；
整数是没有小数点的；
整数可以是正数或负数；
整型可以用三种格式来指定：十进制， 十六进制（ 以 0x 为前缀）或八进制（前缀为 0）；

3.浮点型：与C语言类似，可以是小数，或者使用E来表示的数值；

#### 比较

两种比较方式：

== :只比较值；

===：比较值和类型；

详细的不同类型数据之间的比较见：https://www.runoob.com/php/php-types-comparisons.html

#### 设置常量

define (标识符，常量值，(true/false))

标识符和常量值都需要用双引号包括，常量名不需要加$修饰符，常量可以有英文，下划线，数字，但不可以用数字开头。true代表大小写表示同一常量，默认为false。

#### 字符串变量

定义方式：$(char_name)="....";

*可以使用'.'并置多个字符串；

strlen函数：返回字符串长度，和C语言内该函数作用相同。

strpos函数：用来查找一段指定的文本，如果找到则返回第一个字符在字符串中的位置；未找到则返回FALSE。

*字符串中每个中文字占三个字符长度（UTF-8），在有些时候则是两个字符长度（GBK）。

#### 与C语言运算符不同的运算符

!=和<>等价，均为不等于（值）；

！==必须值相等且类型相同才返回false，否则返回true；

*逻辑运算符xor:A和B有且仅有一个为true才返回true；

*组合比较符：$c = $a <=> $b;

如果a大于b则输出1，等于输出0，小于输出-1；

#### 数组

数组初始化：array();

人工分配id（这一行为在C中并不存在）：$array_name[n]="variable_name"；n表示你想分配的数组id；

得出数组长度：count($array_name);

关联数组：自定义键值对对应关系的数组。

有以下两种初始化方式：

```php
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
```

```php
$age['Peter']="35";
$age['Ben']="37";
$age['Joe']="43";
```

数组的循环输出

可以使用foreach函数：

```php
foreach ($array_name as $key => $value)
foreach ($array_name as $value)
```

使用key,next()函数构建循环：

```
while ($key = key($array_name)) {
printf("%s
", $key);
next($array_name);
}
```

参考文档：

https://blog.csdn.net/weixin_32723617/article/details/115158267

https://m.php.cn/article/399438.html

### 正式学习

#### PHP的标记方式

1.ASP： <% PHP代码 %>

2.短标记：\<? PHP代码 ?>

3.脚本标记：\<script language="php"> PHP代码 \</script>

4.标准标记：\<?php PHP代码 ?>

在PHP5中的PHP.ini文件中可以选择是否开启ASP标记，相关代码如下：

```php
; This directive determines whether or not PHP will recognize code between
; <? and ?> tags as PHP source which should be processed as such. It is
; generally recommended that <?php and ?> should be used and that this feature
; should be disabled, as enabling it may result in issues when generating XML
; documents, however this remains supported for backward compatibility reasons.
; Note that this directive does not control the <?= shorthand tag, which can be
; used regardless of this directive.
    #上面一段说的是推荐使用标准标记，而短标记则会在不久的以后被废除；需要注意的是<?="">的使用是不受限制的（很熟悉对吧，这就是前期sql注入的核心，通过id构造闭合，形成被php执行的代码）
; Default Value: On
; Development Value: Off
; Production Value: Off
; http://php.net/short-open-tag
short_open_tag = On #控制是否适用短标记

; Allow ASP-style <% %> tags.
; http://php.net/asp-tags
asp_tags = Off #控制是否使用ASP标记 
```

需要注意的是，脚本标记在PHP7以后的版本中都不再被使用。

#### PHP分隔符

';'：用来说明一个语句结束。

需要注意的是，多数情况下省略"?>"也可以正常解析PHP语法；且不写相对于写的时候服务器传给浏览器的回显中代码空行会减少（打?>时在回显结束后会有几行空行，而不打则没有）

#### 变量

定义变量：$var_name = var_value;

访问变量：echo $var_name；

删除变量：unset $var_name;

预定义变量（选取重要的罗列）

$_GET：以GET方式提交的数据

$_POST：以POST方式提交的数据

$_REQUEST：只要是提交的数据都在这里

$_GLOBALS:全局变量

$_SESSION：session数据

$_COOKIE：cookie数据 

可变变量：如果一个变量保存的值正好是另外一个变量的名字，那么可以通过·访问一个变量的到另一个变量的值，方法是用两个$.

e.g.:

```php4
<?php
    $a='b';
    $b='CXK';
    echo $$a;
?>
```

输出结果是CXK.给人的感觉类似于C语言中的指针变量。$$a可以理解为$($a),PHP引擎会先解析括号内的内容。

变量传值：

两种方式，值传递和引用传递。

 值传递：将变量值复制后赋值给另一个变量，两个变量互相独立。

引用传递：将变量保存的值所在的内存地址传递给另一个变量：两个变量指向内存中的同一区域内的存储值（其实就是C语言中的指针，PHP以C为基础开发）