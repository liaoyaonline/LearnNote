# SSL双向验证握手详解
## SSL双向验证
握手阶段主要解决两个问题，第一个就协议算法达成一致。第二个是生成一个只有双方知道的加密密钥(对称)
密钥导出函数，在确定协议通信协议的时候也确定了密钥导出函数，SSL和TLS的密钥导出函数是不同的。但大致都是根据三个输入数据得到一个输出数据作为对称密钥。
### 握手流程图
![](/home/liaoya/图片/SSL单向验证.png) 
### 握手步骤详解
简单来说，SSL单向验证握手总共以下几步:

- `client_hello->`
	- 发出通信请求，将自己的支持的算法套件列表发给server，发送随机数A，用于后续密钥计算。

![](/home/liaoya/图片/SSL/client_hello.PNG) 

- `<-server_hello`
	- server回复自己选择的算法套件，发送自己的证书来让client进行校验，发送随机数B，用于后续密钥计算。发送结束标志，告诉client此次通信没有拓展。

![](/home/liaoya/图片/SSL/server_hello.PNG) 

![](/home/liaoya/图片/SSL/certificate,serverkeyexchange,serverhellodone.PNG) 

- `client校验证书`
	- client校验从server处收到的证书，首先根据证书明文的签发者，从可信证书列表中寻找出对应者，根据第二步协商的加密算法，使用该签发者的公钥对证书中的签名摘要进行解密，然后根据第二步协商的摘要算法对证书的明文信息进行摘要，对比解密后的摘要和自行摘要，如果一致，证明了这个证书是由可信的机构签发的，证书信息没有被修改。然后从RCL或者OCSP(严格程度由client自行决定)处查询该证书是否被注销啥的，通过之后，根据证书的明文信息校验该证书是否过期，该证书的域名和当前访问的域名是否一致。



- `client->`
	- client根据第二步协商的算法从证书中提取到的公钥对随机数C进行加密，发送给server。
	- client根据第二步协商的协议选择密钥导出函数，根据随机数A,B,C生成对称密钥。将该密钥对之前几步消息的hash值进行加密，发送给server用于校验密钥正确，信道安全。
	- 发送消息，后续将不再明文通信。

![](/home/liaoya/图片/SSL/client_key_exchange.PNG) 
![](/home/liaoya/图片/SSL/Encrypted_Handshake_Message.PNG) 

- `<-server`
	- server用自己私钥将上一步client发送过来的加密后的随机数C进行解密，根据第二步协商的协议选择密钥导出函数，根据随机数A,B,C生成对称密钥，使用该密钥对上一步client发送过来的加密信息进行解密，然后对比自己之前几步的消息的hash值，一样说明server密钥正确，`client->server`信道安全。
	- server将自己生成的密钥对之前通信的hash值进行加密，发送给client，用于校验密钥正确，信道安全（主要防止攻击者在第一步时修改明文消息，比如删除算法套件列表中的强算法套件，提取所有上面握手步骤中信息的hash值来保证server之前收到的信息和client发送的消息是一致的，中途没有被修改过)

![](/home/liaoya/图片/SSL/server_encrypted_handshake_message.PNG) 


- `client`
	- 用自己生成的密钥解密上一步server发送过来的消息，对比之前通信的hash值，如果一样，说明client与server密钥一致，`server->client`信道安全。后续将正式通信。