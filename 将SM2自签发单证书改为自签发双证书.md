# 将SM2自签发单证书改为自签发双证书
## 从req到证书生成(以SM2证书为例)
* 生成`req`后在证书管理界面点击自签发证书进行自签发证书。

* 在`CertModel.php`文件中调用类`SiteCertModel`的函数`onSelfSign`对`req`进行处理。

* 在确定该`req`对应路径`/cfg/GAD/cert/site`内的`csr`文件和`key`文件都存在，且该证书尚未签发，调用函数`CertificateHelper::signCSR`进行签发证书。

* 函数`CertificateHelper::signCSR`的原型如下所示，其中`cert`是xx,`key`是`key`，`csr`是`req`文件路径，`day`是有效期
```
static public function signCSR($cert, PrivateKey $key, $csr, $days = 365)
```

* 函数`CertificateHelper::signCSR`先对`key`类型进行判定，判定语句如下所示，该语句判断`key`是否是类`ECPrivateKey`的实例化，而`key`是否实例化在文件`CertModel.php`中使用语句`$key = PrivateKeyFactory::createFromFile($keyPath); `进行定义，而`createFromFile`函数则是根据判定`keypath`路径对应的`key`文件中是否存在`EC PRIVATE KEY`字符串来决定是否将其定义为类`ECPrivateKey`的实例化。
```
if ($key instanceof ECPrivateKey)
```
* 将`key`相关信息从`key`中提取出来保存到`details`上，对`details`类型进行判定，判定语句如下，如果该类型为`CN-GM-ECC`（即国密，`sm2`属于国密）。
对`configargs`进行设定，`configargs`是配置项，用来初始化请求。
```
if ($details["ec"]["curve"] == "CN-GM-ECC") 
```
* 对`req`进行证书生成，其语句如下所示。其中`csr`是`req`文件路径,`null`是`cacert`路径，但是为`null`就属于自签发。`key->getkey()`就是`key`，`days`是有效期设定。`configargs`是配置项，用来初始化请求。
```
$certRes = @openssl_csr_sign($csr, null, $key->getKey(), $days, $configargs);
```
* 将证书保存巴拉巴拉巴拉。。。。。。。。。
## 将自签发单证书修改为自签发双证书(以SM2证书为例)
### 双证书和单证书区别
&emsp;&emsp;一般而言，非对称加密，可以用私钥进行签名，公钥进行加密，也就是说单证书本身既可以充当加密证书，又可以充当签名证书，前者别人使用公钥加密，用自己的私钥进行解密。后者自己使用私钥签名，别人使用公钥进行验证。

&emsp;&emsp;那么为什么会有双证书呢，总而言之，国家需要，即国家需要对你的通信加密部分进行解密。所以就将证书分开了，一个签名证书，一个加密证书。

&emsp;&emsp;签名证书，是自己生成的，私钥由自己保管，加密证书是`ca`生成的，私钥`ca`也知道。

&emsp;&emsp;具体到`SP4`上面，先在生成证书请求界面，填写选项，生成一个证书请求，然后将证书请求下载下来，发送给`ca`，然后该`ca`做事了，`ca`一方面根据你的请求文件，生成签名证书，另一方面，从你的请求文件里面提取`DN`项目，其实就是你之前填写的选项，根据`DN`项再生成一个请求文件，然后根据新的请求文件生成加密证书，`ca`会将加密证书的私钥做个备份，然后把四个文件发还给你，即签名证书+对应私钥，加密证书+对应私钥。。。。

&emsp;&emsp;那么问题来了，为什么要再生成一个请求文件呢?签名证书和加密证书在请求文件中的唯一区别就是`keyUsage`选项的区别。以下是加密证书和签名证书的请求文件`v3_req`拓展项部分内容：
```
[ v3_req ]
# Extensions to add to a certificate request
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, keyEncipherment, dataEncipherment

[ v3_req ]
# Extensions to add to a certificate request
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature
```
### SP7的SM2证书生成过程
* 得到请求后，先取出`req`文件的地址，然后对`req`文件使用命令行调用包含`openssl`命令的脚本直接生成证书，其命令如下所示。
```
exec("sh /kssl/WEBUI/bin/GenerateSM2Cert.sh $SITE_CERT_DIR/$certId.req $SITE_CERT_DIR/$certId.pem", $result, $ret);
```
其调用的脚本`GenerateSM2Cert.sh`内容如下所示。
```
#!/bin/sh
reqFile=$1;
pemFile=$2;
SITE_CERT_DIR="/kssl/HRP/cfg/default/pem";
if [ "$reqFile" = "" ] || [ "$pemFile" = "" ]; then
        echo "usage: GenerateSM2Cert.sh reqFile pemFile";
        exit 1;
fi;
cnfFile=${reqFile%.req}.cnf
openssl x509 -req -in $reqFile -CA $SITE_CERT_DIR/localca/TrustSM2Root.cer -CAkey $SITE_CERT_DIR/localca/TrustSM2Root.key -out $pemFile -CAserial $SITE_CERT_DIR/localca/TrustSM2Root.srl -days 3650 -extfile $cnfFile -extensions v3_req 2>&1;
```
### 修改思路

根据对SP7证书生成过程，即在生成`req`文件后再调用函数`openssl_csr_new`生成对应的`csr`文件，然后对该文件进行处理生成加密证书和签名证书。
* 生成加密证书，使用函数`openssl_csr_get_subject()` 返回`csr`中专有名称信息的主题，其中包含了通用名称 `(CN)`, 机构名称 `(O)`, 国家名 `(C) `等字段。。。。提取到这些信息后，使用函数`openssl_pkey_new`生成一对非对称密钥，利用函数`openssl_csr_new`生成证书签发请求，使用函数`openssl_csr_sign`签发证书，将证书保存
* 生成签名证书，