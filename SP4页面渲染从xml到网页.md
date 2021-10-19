# 页面渲染流程
## 流程追踪(以客户端代理服务基本参数页面为例)
* 通过`URL`请求，查找到对应`php`页面`/GAD/www/module/srv/sct/service_basic.php`。

* 在该`php`文件中找到由配置文件构建的控件列表的函数`addWicket`

* 对`addWicket`函数中调用的参数`$wf->createHiddenWicket('opType',$opType)`进行追踪。

* `createHiddenWicket`函数定义如下所示。

* 追踪`loadValue`函数，该函数定义如下所示。

* 追踪`loadValue`函数中的`$this->getModel()->get($name)`函数，通过阅读类`WicketFactory`的定义可知，`model`是由调用`WicketFactory`的时候被外部输入的。

* 回到`/GAD/www/module/srv/sct/service_basic.php`，查到定义`model`处。

* 追踪`ServiceBasicParamModel`类的定义，找到其中`get`函数

* 追踪`$this->basicParamConfig->getValue($key)`，发现`basicParamConfig`属于`SCTServiceBasicXmlConfig`的实例化，在`SCTServiceBasicXmlConfig`类中没有找寻到`getValue`的定义，向它的父类`XMLConfigEx`寻找，在`XMLConfigEx`中没有找到`getValue`的定义,继续向其父类`XMLConfig`寻找，找到`getValue`定义，其定义如下所示。

* 追踪`getNode`函数，其函数定义如下所示。其中`$comparator = new XMLNodeValueComparator($name, $this->encodeText($value));`语句是进行初始化，没有对`name`对应的`value`赋值。

* 追踪函数`traverseNode`。其定义如下所示。


* 追踪函数`load`，其定义如下所时。该函数内通过`$xml = $this->loadXML($this->getXmlPath());`语句读取`name`对应的`xml`文件。然后从文件中选择出和`name`对应的`value`进行赋值。


## 流程总结

* `load`函数将xml文件读取为字符串，并将里面的`name`对应的`value`取出来保存为`node`。

* 然后使用`getNodeValue`对取出来的`value`进行解码。

* 最后通过`createHidderWicket`函数创立`Wicket`

* 将所有`Wicket`都加入`frmWicket`中。

* 然后将`frmWicket`输出渲染为页面