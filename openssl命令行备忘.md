# openssl命令行备忘
## 一些问题
* 什么是国密的双证书？
双证书即两套证书，在以往的知识学习中，我们知道对于非对称加密会产生一个公钥和一个私钥，一般而言，我们使用公钥进行加密，然后用私钥进行签名，或者使用私钥进行签名(加密)，公钥进行验证。双证书即使用一个key生成两套证书（具体是使用`openssl`的`v3`拓展项，在配置文件中指定证书用途，来生成不同的证书)，一套证书只用来加密，一套证书只用来签名，对于客户端来说，需要保留加密的证书（公钥)和签名的证书（私钥+)
## openssl命令行
* 查看证书配置信息
```
openssl x509 -in xxxx.pem -text
```
* SM2证书签发相关
```
# 产生国密曲线的密钥对
openssl ecparam -name SM2 -genkey -out sm2-test.key -noout

# 构造国密证书请求（签名算法为SM2+SM3）
openssl req -new -key sm2-test.key -config sm2-test.cnf -out sm2-test.req -sm3

# 自签发SM2证书（签名算法为SM2+SM3）
openssl x509 -req -in sm2-test.req -signkey sm2-test.key -out sm2-test.pem -sm3

# 通过CA证书签发SM2证书（签名算法为SM2+SM3）
openssl x509 -req -in sm2-test.req -CA sm2-ca.pem -CAkey sm2-ca.key -out sm2-test.pem -sm3 -CAcreateserial
```
> 使用openssl命令行产生双证书的关键是在于构建证书请求时对证书请求配置进行修改，即配置文件中的`keyUsage`，当该证书做为签名证书使用时，一般配置为
```
keyUsage = nonRepudiation, digitalSignature
```
> 当该证书作为加密证书使用时，一般配置为
```
keyUsage = nonRepudiation, keyEncipherment, dataEncipherment
```
* 将pem转化为p12
```
openssl pkcs12 -export -inkey mysite.key -in mysite.pem -nodes -out mysite.p12  (输出不带口令的p12证书)
```
* 生成v3的证书命令
```
openssl x509 -req  -days 365 -sha256 -extfile openssl.cnf -extensions v3_req   -in server.csr -signkey server.key -out server.crt
```