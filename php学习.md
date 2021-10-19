# PHP学习
## 关键命令
* `var_dump();`//用于输出目标结构内容
```
print "<pre>";
var_dump($name);
print "</pre>";//一般配合print"<pre>"，格式化输出，比较容易看
```
* `instanceof`//用于判定某个对象是否是某个类的实例化
```
if ($key instanceof ECPrivateKey)
{
.....
}//判断key是否是ECPrivateKey的实例化
```