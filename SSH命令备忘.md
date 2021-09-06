# ssh命令备忘
## 应用场景
> sudo /etc/init.d/ssh restart//执行命令的前提是客户端和服务端都打开了ssh
> scp -r ssl@192.168.190.7:/kssl/GAD ./

客户端使用这个命令从IP地址的文件夹复制到本地
前提是客户端SSH服务打开
> sudo /etc/init.d/ssh start

服务端开机密码:`K0a120110601v6`用户名`ssl`
K0a@120110

> 必须修改为固定iP,192.168.190.7

## 错误解决
```
ssh_exchange_identification: read: Connection reset by peer
```
编辑配置文件
```
vi /etc/hosts.allow
```
在配置文件里面添加
```
sshd: ALL
```
systemctl restart sshd
```
ssh: connect to host 192.168.190.7 port 22: Connection refused
```
可能是端口问题，即默认端口不允许ssh接入，要么sshd配置文件开放默认端口，要么在客户端连接的时候指定允许的端口。
```
scp -r -P 60022 ssl@192.168.190.7:/kssl/SITE/test_SM2/test_double_sm2 ./

```