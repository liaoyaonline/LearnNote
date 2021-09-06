# 去掉SCT的硬件加速选项
## 问题现象
[去掉SCT的硬件加密引擎选项](http://10.0.1.8/mantis/view.php?id=59873)
## 问题排查
通过阅读代码，发现是在`\cfg\GAD\web\module\srv\sct\service_basic.v.xml`对SCT界面基本参数进行配置。
## 问题解决
注释掉硬件加密引擎相关`field`
代码截图：

## 测试
未修改前`SCT`基本参数界面截图：
修改后`SCT`基本参数界面截图：