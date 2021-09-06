# 银企直连的SSL session有效期和缓存大小做成界面可配置
## 问题现象
[银企直连的SSL session有效期和缓存大小做成界面可配置](http://10.0.1.8/mantis/view.php?id=59831)
## 问题排查
通过参考`SP4`历史提交记录[增加OCSP站点证书验证](http://git.koal.com/gw-server/upgrade/2021-01/SSL6.1.4_SP4/-/merge_requests/34/diffs#5b6ee2bc7ca701dc2885c742adeb22067ffabf73)推断出在银联高级配置界面增加功能主要修改以下几个文件:

* `cfg/GAD/web/module/srv/shb2c/resource.ini`

* `cfg/GAD/web/module/srv/shb2c/service_param.v.xml`

* `cfg/SHB2C/default/xslt/basic.conf.xslt`

* `cfg/SHB2C/default/basic.xml`

* `kssl/GAD/www/module/srv/shb2c/model/ServiceAdvanceParamModel.php`

* `kssl/GAD/www/module/srv/shb2c/resource.ini`

*  `kssl/GAD/www/module/srv/shb2c/service_param.v.xml`
## 问题解决
在上述文件中增加`session_timeout`和`session_cache`选项
`resource.ini`代码修改截图
`service_param.v.xml`代码修改截图
`ServiceAdvanceParamModel.php`代码修改截图
高级配置界面截图：

修改选项高级配置界面截图：

## 测试
