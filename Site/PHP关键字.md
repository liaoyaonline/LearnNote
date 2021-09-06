# PHP学习
`php`文件是从上到下执行的，开始部分是函数的定义，接下来是`switch`捕捉传送过来的数据，使用`case`语句对其进行处理，再下来是页面的渲染和设定，包括捕获外来输入数据，调用定义的函数。
## 诊断测试
> php -f index.php //这个命令用来测试是否有什么语法错误

函数

> debug_print_backtrace(); //打印出一个页面的调用过程

变量
> var_dump($x); //返回变量的数据类型和值；
## 标签
* `<script>`
	* `type` 里面值表示`MIME-type`指示脚本的`MIME`类型
	* `async`里面的值表示`async`表示规定异步执行脚本，仅适用于外部脚本。
	* `charset`里面的值表示`charset`规定在外部脚本文件中使用的字符编码
	* `defer`里面的值表示`defer`规定是否对脚本执行进行延迟，直到页面加载为止
	* `language`里面的值表示`script`不赞成使用，规定脚本语言，请使用type属性代替它。
	* `src`里面使用`URL`规定外部脚本文件的`URL`
	* `xml:space`里面有`preserve`表示规定是否保留代码中的空白
* `$_FILES`

* form
	* `accept`-`MIME_type`-`HTML 5`不支持
	* `accept-charset`-`charset_list`-规定服务器可处理的表单数据字符集
	* `action`-`URL`-规定当提交表单时向何处发送表单数据
	* `autocomplete`-`on/off`-规定是否启用表单的自动完成功能
	* `enctype`-见说明-规定在发送表单数据之前如何对其进行编码。
	* `method` - `get/post`-规定用于发送`form-data`的`HTTP`方法。
	* `name`-`form-name`-规定表单的名称
	* `novalidate`-`novalidate`-如果使用该属性，则提交表单时不进行验证。
	* `rel`-`external/help/license/next/nofollow/noopener/noreferrer/opener/prev/search/`-规定链接资源和当前文档之间的关系。
	* `target`-`_blank/_self/_parent/_top/framename`-规定在何处打开`action URL`
	
* input
	* `type`-`button/checkbox/file/hidden/image/password/radio/reset/submit/text`-规定`input`元素的类型
	* `name`-`field_name`-定义`input`元素的名称
	* `value`-`value`-规定`input`元素的值	
## 关键字
其结构如下
```
Array
(
    [userfile] => Array
    (
        [name] => filename.png
        [type] => image/png
        [tmp_name] => /tmp/phps93PyL
        [error] => 0
        [size] => 344925
    )
)
```
* $_GET 该变量接受所有以get方式发送的请求。

* $_POST 该变量接受所有以post方式发送的请求。

* _REQUEST 支持get和post发送过来的请求

* global $x,$y //在函数内调用函数外定义的全局变量x,y。

* $GLOBALS['y] = $GLOBALS['x] + $GLOBALS['y'];//调用全局变量x和y

* static $x = 0;//定义一个局部变量，并且希望这个局部变量在函数结束后不要删除

* $name = "runoob";$a=<<<EOF"abc"$name"123"
EOF;//EOF必须以;结尾，且独立一行，可以使用其他字符替代，其作用是将上述字符串整合成一个字符串。

* var_dump($x); //返回变量的数据类型和值；

* strlen()//返回字符串长度；

* echo '"'. __FILE__ .'"' ;//返回文件的绝对路径

* echo '"'. __DIR__ .'"' ; //返回目录所在路径

* echo '"'. __FUNCTION__ .'"' ;//返回当前函数名

* echo '类名为：'.__CLASS__."<br>";//返回当前类名

* void __construct ([ mixed $args [, $... ]] )//构造函数，用来初始化
```
function __construct( $par1, $par2 ) {
   $this->url = $par1;
   $this->title = $par2;
}
```
> $runoob = new Site('www.runoob.com', '菜鸟教程');

就可以直接初始化了

* void __destruct ( void )//对象结束时执行


* echo strpos("Hello world!","world");//查找world，找到返回第一个匹配的字符位置，没找到返回false.

* bool define ( string $name , mixed $value [, bool $case_insensitive = false ] )//name是常量名，value是字符串内容,case_insensitive是可选参数，默认false,为true为大小写敏感