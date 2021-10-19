# home
## nginx常用命令
```
nginx -t #用于检测nginx的配置文件是否有错误，会显示配置文件路径
nginx -s reload # 重新加载配置文件
tail -f filename#动态查看文件后面10行，用于debug
echo $?#查看上一个命令是否执行成功
ps aux | grep nginx | greo –v grep#查看nginx进程是否启动。此命令用在代码判断nginx进程是否启动，如果只用ps aux | grep nginx 即使没有启动也会用内容返回，影响判断
```
## nginx配置文件结构
- 全局块
	- 配置影响nginx全局的指令，一般运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成的work process数。
- events块
	- 配置影响nginx服务器或与用户的网络连接。
- http块
 	- 可以嵌套多个server,配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。
 - server块
 	- 配置虚拟主机的相关参数
 