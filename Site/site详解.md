# site源码
## 代码详解
## 页面流程
第一步，初始化wf结构体
第二步，渲染页面，根据页面的提交进行处理
```
$model = new SiteCertModel(new SiteStore());
$wf = new WicketFactory($model);
```
`$wf`的结构体内容如下所示
```
object(WicketFactory)#37 (2) { ["wicketList":"WicketFactory":private]=> array(0) { } ["model":"WicketFactory":private]=> object(SiteCertModel)#3 (6) { ["store":"CertModel":private]=> object(SiteStore)#4 (1) { ["baseDir":"Store":private]=> string(18) "/cfg/GAD/cert/site" } ["operationName":"ListModel":private]=> NULL ["operationDesc":"ListModel":private]=> NULL ["preInterceptors":"InterceptorContainer":private]=> array(0) { } ["postInterceptors":"InterceptorContainer":private]=> array(0) { } ["exceptionInterceptors":"InterceptorContainer":private]=> array(0) { } } }
```


```
if (is_uploaded_file($_FILES['certfile']['tmp_name']))
```
其中，`_FILES`的数组内容是
```
array(1) { ["certfile"]=> array(5) { ["name"]=> string(11) "sm2-enc.pfx" ["type"]=> string(20) "application/x-pkcs12" ["tmp_name"]=> string(14) "/tmp/php1jgiE3" ["error"]=> int(0) ["size"]=> int(1074) } }
```
```
$_REQUEST["certfile"] = $_FILES['certfile']['tmp_name'];
$_REQUEST["certfile_name"] = $_FILES['certfile']['name'];
```
`_REQUEST`的数组内容是：
```	
	array(6) { ["module"]=> string(4) "cert" ["group"]=> string(2) "ca" ["view"]=> string(4) "site" ["opType"]=> string(10) "importCert" ["certfile_view"]=> string(23) "C:\fakepath\sm2-enc.pfx" ["passwd"]=> string(0) "" } array(8) { ["module"]=> string(4) "cert" ["group"]=> string(2) "ca" ["view"]=> string(4) "site" ["opType"]=> string(10) "importCert" ["certfile_view"]=> string(23) "C:\fakepath\sm2-enc.pfx" ["passwd"]=> string(0) "" ["certfile"]=> string(14) "/tmp/phpKb4JR7" ["certfile_name"]=> string(11) "sm2-enc.pfx" }
```
## 常用命令
`type="hidden"`表示隐藏