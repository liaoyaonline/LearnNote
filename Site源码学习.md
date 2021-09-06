# Site.php学习
> $model = new SiteCertModel(new SiteStore());

## SiteCertModel类继承
SiteCertMode的类结构如下所示:
```
class SiteCertModel extends CertModel {
	
	public function listCertificates() {
		$certs = $this->getStore()->listCertificates();
		$csrs = $this->getStore()->listCSRs();
		
		/**
		 * 	注意array_merge的参数顺序：
		 * 	如果certs中存在于csrs元素相同key值的项，csrs中的值会被覆盖
		 * 	也即如果存在站点证书，则相同ID的证书请求数组元素会被其覆盖
		 */
		return array_merge($csrs, $certs);
	}
	
	public function getCertificateType($cert) {
		if ($cert instanceof CertificateRequest) {
			return "证书请求";
		}
		else {
			$string = "";
			$purposes = $cert->getPurposes();

			if($purposes['sslserver'])
				$string = $string . "/站点证书";
			if($purposes['sslclient'])
				$string = $string . "/用户证书";
			
			return substr($string,1);
		}
	}
	
	protected function handleStatus($cert, $bean) {
		if ($cert instanceof CertificateRequest) {
			$array = (array)$cert;
			foreach ($array as $k => $v)
			{
				$string = $v;
				break;
			}
  
			if(file_exists($string.".temp"))
				$bean->status = "请上传CFCA签名证书";
			else
				$bean->status = "等待签发";
		}
		else {
			$bean->hasCSR = $this->getStore()->findCSRFileById($cert->getId());
			parent::handleStatus($cert, $bean);
		}
	}
	
	/**
	 * 重载 ListModel的 support 方法
	 * @param $cmd
	 * @return true/false
	 */
	public function support($cmd) {
		if (in_array($cmd, array("importCert", "deleteCert", "selfSign"))) {
			return true;
		}
		
		return parent::support($cmd);
	}
	
	/**
	 * 重载 ListModel 的 ctrl 方法
	 * @param $cmd		控制命令
	 * @param $params	命令的参数
	 */
	public function ctrl($cmd, $params = array()) {
		switch ($cmd) {
		case "importCert":
			$this->onImportCert($params);
			break;	
		case "deleteCert":
			$this->onDeleteCert($params);
			break;	
		case "selfSign":
			$this->onSelfSign($params);
			break;	
		default:
			return parent::ctrl($cmd, $params);
		}	
	}

	public function onDeleteCert($params) {
		parent::setOperationName("删除证书");

		$idArray = $params["id"];
		$certPaths = Array();
		
		$store = $this->getStore();
		foreach ($idArray as $id) {
			$certPath = $store->findCertFileById($id); 
			if ($certPath) {
				$interceptor = new CertDependencyChecker($store);
				$interceptor->onDelete($certPath);
			}
			else {
				$certPath = $store->findCSRFileById($id);
			}
			
			if ($certPath) {
				$store->delete($certPath);
				array_push($certPaths, $certPath);
			}
		}

		parent::setOperationDesc("删除证书文件：" . implode(',', $certPaths) . ' ');
	}
	
	public function onImportCert($params) {
		parent::setOperationName("导入站点证书");
		
		$certPath = $params['certfile'];
		$fileName = $params['certfile_name'];
        $certPass = $params['passwd'];
		
		$store = $this->getStore();
		if ("p12" == CertificateHelper::detectFormat($certPath, $fileName)) {
			$store->importSiteCertP12($certPath, $certPass);
		} else if("skf" == CertificateHelper::detectFormat($certPath, $fileName)) {
			$store->importSiteKeySKF($certPath, $certPass);
		} else {
			$store->importSiteCert($certPath, $certPass);
		}
	}
	
	public function onSelfSign($params) {
		parent::setOperationName("自签发站点证书");
		
		$id = $params["reqId"];
		
		$store = $this->getStore();
		
		$csrPath = $store->findCSRFileById($id);
		if ($csrPath == null) {
			throw new Exception("无法找到ID为" . $id . "的证书请求");	
		}
		
		$keyPath = $store->findKeyFileById($id);
		if ($csrPath == null) {
			throw new Exception("无法找到ID为" . $id . "的证书请求对应的私钥");	
		}

		$certPath = str_replace("." . $store->getCSRExt(), 
								"." . $store->getCertificateExt(), 
								$csrPath);
		if (file_exists($certPath)) {
			throw new Exception("ID为" . $id . "的站点证书文件" . $certPath . "已存在");	
		} 
		
		$key = PrivateKeyFactory::createFromFile($keyPath); 
		
		/*	有效期	*/
		$days = intval(_T("site_req", "selfsign_days", dirname(dirname(__FILE__))));
		if ($days < 1) {
			$days = 1;
		}
		
		$cert = CertificateHelper::signCSR(null, $key, "file://" . $csrPath, $days);
		openssl_x509_export_to_file($cert->getCertificate(), $certPath);

		parent::setOperationDesc("自签发站点证书：" . $certPath . ' ');
	}
}


```
SiteCertModel是继承CertModel类的，CertModel的类的定义如下：
```
class CertModel extends ListModel implements IOperateLoggerModel {
	private $store;
	
	public function __construct($store) {
		$this->store = $store;
	}
	
	/**
	 * @return the $store
	 */
	public function getStore() {
		return $this->store;
	}

	/**
	 * @param $store the $store to set
	 */
	public function setStore($store) {
		$this->store = $store;
	}

	public function elementNew() {
		return new CertBean();
	}
	
	public function listCertificates() {
		return $this->getStore()->listCertificates();
	}
	
	public function getCertificateType($cert) {
		return $cert->isSelfSign() ? "根CA" : "下级CA";
	}
	
	public function getKeyUsage($purpose) {
		if(strstr($purpose['keyUsage'],"Signature") && strstr($purpose['keyUsage'],"Encipherment"))
			return "[单证书]";
		if(strstr($purpose['keyUsage'],"Signature"))
			return "[签名证书]";
		if(strstr($purpose['keyUsage'],"Encipherment"))
			return "[加密证书]";
		return "[单证书]";
	}

	public function createCertBean($cert) {
		$bean = new CertBean();
		$bean->id = $cert->getId();	
		$bean->type = $this->getCertificateType($cert);
		$bean->keyType = $cert->getKeyType();	
		$bean->issuer = $cert->getIssuerName();	
		$bean->name = $cert->getSubject("CN");
		
		if (!$bean->name) {
			/*	CN为空时将整个DN作为证书名称	*/
			$bean->name = $cert->getSubject(null);
		}
		if($this->getStore() != NULL){
			if(!$this->getStore()->checkCertHaveKey($cert->getId())){
				$bean->name.="<font color=red>[站点证书不存在对应的私钥文件,或链接已失效]</font>";
			}
		}
		$subjectAltName = $cert->getExtensions("subjectAltName");
		$bean->subjectAltName = $subjectAltName["subjectAltName"];
		$bean->level = $cert->level;
		$bean->notBefore = $cert->getNotBeforeText("Y-m-d");	
		$bean->notAfter = $cert->getNotAfterText("Y-m-d");
	
		$bean->description = $cert->getDescription();
		$bean->purposes = $cert->getPurposes();
		
		$bean->keyUsage = $this->getKeyUsage($bean->purposes);
		$bean->hash = $cert->getHash();
		$bean->validTo = $cert->getNotAfter();
		$bean->validFrom = $cert->getNotBefore();

		$string = "";
		$purposes = $cert->getPurposes();
		if($purposes['sslserver'])
			$string = $string . "/站点证书";
		if($purposes['sslclient'])
			$string = $string . "/用户证书";
		if($purposes['any'])
			$string = $string . "/站点证书". "/用户证书";
		$bean->CertUsage = substr($string,1);
		
		$bean->dependency = $this->checkDependency($bean->id);
		return $bean;
	}
	
	public function lists($beginPos = 0, $endPos = 0x7FFFFFFF) {
		$certs = $this->listCertificates();
		// 若是CA证书构造的该函数，不需要判断双证和过期日
		// 来自升级包 SSL6.1.4_SP3_cert-quick-replacement 的合并
		if ($this->getStore() instanceof CaStore) {
			$results = array();
			foreach ($certs as $cert) {
				$bean = $this->createCertBean($cert);
				$this->handleStatus($cert, $bean);
				array_push($results, $bean);
			}
			return $results;
		}

		// 用于存放双证的数组
		$sigCerts = array();
		$encCerts = array();

		// 根据有效期和当前时间的关系不同进行存放的数组
		$ineffectiveCerts = array();
		$expiredCerts = array();
		$temporaryCerts = array();
		$effectiveCerts = array();
		$otherCerts = array();

		foreach ($certs as $cert) {
			$bean = $this->createCertBean($cert);
			$this->handleStatus($cert, $bean);
			//按照证书私钥用途将证书分开
			if($bean->keyUsage == "[加密证书]"){
				array_push($encCerts, $bean);
            }else{
				array_push($sigCerts, $bean);
            }
		}

		//从签名证书中生成显示列表
        foreach($sigCerts as $sig) {
            // 设置签名证书bean中加密证书的信息
            foreach($encCerts as $enc) {
                if($sig->hash == $enc->hash
                    && $sig->validTo == $enc->validTo
                    && $sig->validFrom == $enc->validFrom ) {
                    $sig->id2 = $enc->id;
                    $sig->keyUsage2 = $enc->keyUsage;
                    $sig->CertUsage2 = $enc->CertUsage;
                }
			}
			switch ($sig->status) {
				case "尚未生效":
					array_push($ineffectiveCerts, $sig);
					break;
				case "已过期":
					array_push($expiredCerts, $sig);
					break;
				case "快过期":
					array_push($temporaryCerts, $sig);
					break;
				case "有效":
					array_push($effectiveCerts, $sig);
					break;
				default:
					array_push($otherCerts, $sig);
			}
        }		

		return array_merge(
			$this->sortCerts($temporaryCerts),
			$this->sortCerts($effectiveCerts),
			$this->sortCerts($expiredCerts),
			$this->sortCerts($ineffectiveCerts),
			$this->sortCerts($otherCerts)
		);
	}

	/**
	 * @todo 升序排列未实现在代码中
	 * @param CertBean[] $certs
	 * @param int $order
	 * @return mixed
	 */
	public function sortCerts($certs, $order = SORT_DESC)
	{
		if (count($certs) > 1) {
			usort($certs, array("CertModel", "compareStartTime"));
		}

		return $certs;
	}

	/**
	 * @param CertBean $currentCert
	 * @param CertBean $nextCert
	 * @return bool
	 */
	public static function compareStartTime($currentCert, $nextCert)
	{
		return (strtotime($currentCert->notBefore) < strtotime($nextCert->notBefore));
	}

    public function findEncFileByHash($hash) {
        $ext = "pem";
        $files = array();
        $files = glob(ICA_CFG_ENC_DIR . "/$hash.*.$ext");
        if (($files == null)
             || (count($files) == 0)) {
            return null;
        }

        return $files;
    }

    public function findEncKeyByHash($hash) {
        $ext = "key";
        $files = array();
        $files = glob(ICA_CFG_ENC_DIR . "/$hash.*.$ext");
        if (($files == null)
             || (count($files) == 0)) {
            return null;
        }

        return $files;
    }

	protected function handleStatus($cert, $bean) {
		if ($cert->getNotBefore() > time()) {
			$bean->status = "尚未生效";
		} else if ($cert->getNotAfter() < time()) {
			$bean->status = "已过期";
		} else if ($cert->getNotAfter() < (time() + (30 * 24 * 3600))) {
			$bean->status = "快过期";
		} else {
			$bean->status = "有效";
		}
		
		$array = (array)$bean;
		foreach ($array as $k => $v)
        {
            $string = $v;
            break;
        }
		$hash = stristr($string,".",true);
        if(file_exists(ICA_CFG_SITE_DIR . "/".$string.".csr.temp")) {
			if($this->findEncFileByHash($hash)) {
				if($this->findEncKeyByHash($hash)) {
					;
				}
				else {
					$bean->status = ($bean->status)." / 请上传CFCA加密密钥";
				}
			}
			else {
				$bean->status = ($bean->status)." / 请上传CFCA加密证书";
			}
		}
	}
	
	public function checkDependency($id)
	{
		$store = $this->getStore();

		if (!is_object($store)) {
			return false;
		}
		$certPath = $store->findCertFileById($id);

		if (!$certPath) {
			return false;
		}

		$interceptor = new CertDependencyChecker($store);

		try {
			$interceptor->onDelete($certPath);
		}catch (Exception $exception) {
			return true;
		}

		return false;
	}

}

```
CertModel类是继承ListModel,其结构如下：
```
class ListModel extends InterceptorContainer implements IListModel {
	
	//操作日志
	private $operationName;
	private $operationDesc;
	
	public function setOperation($operationName, $operationDesc) {
		$this->operationName = $operationName;
		$this->operationDesc = $operationDesc;
	}
		
	public function getOperationName() {
		return $this->operationName;
	}
	
	public function getOperationDesc() {
		return $this->operationDesc;
	}
	
	public function setOperationName($operationName) {
		$this->operationName = $operationName;
	}
	public function setOperationDesc($operationDesc) {
		$this->operationDesc = $operationDesc;
	}
		
	/*	Implement IModel	*/
	public function support($cmd) {
		switch($cmd) {
		case IListModel::ACTION_INSERT: 
		case IListModel::ACTION_UPDATE: 
		case IListModel::ACTION_DELETE: 
			return true;	
		default:
			return false;	
		}
	}
	
	public function ctrl($cmd, $params = array()) {
		$processed = false;
		
		$eleId = -1;
		$eleIds = null;

		//支持批量操作
		if (array_key_exists(TableColHeader::TAB_ELE_ID, $params)) {
			$eleId = $params[TableColHeader::TAB_ELE_ID];
			if (is_array($eleId)) {
				$eleIds = $eleId;
				$eleId = null; 	
			}
		}
		else if (array_key_exists(TableColHeader::TAB_ELE_IDS, $params)) {
			$eleIds = $params[TableColHeader::TAB_ELE_IDS];
		}
		
		switch ($cmd) {
		case IListModel::ACTION_INSERT:
			$keys = $this->elementKeys();
			$element = $this->elementNew();
					
			/*	利用ObjectWrapper将element封装为一个对象，自适应$element是数组还是对象	*/
			$objWrapper = new ObjectWrapper($element);
					
			/*	设置新Element中属性值	*/
			foreach ($keys as $key) {
				if (array_key_exists($key, $params)) {
					$objWrapper->set($key, $params[$key]);
				}
			}

			$this->insert($element, $eleId);
			
			$processed = true;
			break;
		case IListModel::ACTION_UPDATE:
			$keys = $this->elementKeys();
			
			/*  支持批量更新  */
			if ($eleIds == null) {
				$eleIds = array($eleId);
			}
			
			foreach ($eleIds as $eleId) {
				$element = $this->elementAt($eleId);
				if ($element == null) {
					throw new Exception("位置为" . $eleId . "的元素不存在，无法更新", 10050);
				}
							
				/*	利用ObjectWrapper将element封装为一个对象，自适应$element是数组还是对象	*/
				$objWrapper = new ObjectWrapper($element);
							
				/*	替换该Element中原有的值	*/
				foreach ($keys as $key) {
					if (array_key_exists($key, $params)) {
						$objWrapper->set($key, $params[$key]);
					}
				}
								
				$this->update($element, $eleId);
				$processed = true;
			}
			break;
		case IListModel::ACTION_DELETE:
			/*  支持批量删除  */
			if ($eleIds == null) {
				$eleIds = array($eleId);
			}
			
			foreach ($eleIds as $eleId) {
				$this->delete($eleId);
				$processed = true;
			}
			
			break;
		}
		
		if ($processed) {
			$this->flush();	
		}
		
		return $processed;
	}
	
	/*	Implement IPropertiesModel	*/
	public function size() { 
		throw new Exception("IListModel::size方法尚未实现");
	}
	
	public function elementNew() { 
		throw new Exception("IListModel::elementNew方法尚未实现");
	}
	
	public function elementKeys() { 
		$element = $this->elementNew();
		if ($element == null) {
			return Array(); 
		}
		else if (is_array($element)) {
			return array_keys($element);
		}
		else {
			return ObjectHelper::getPropertyNames($element);
		}
	}
	
	public function elementAt($position) { 
		throw new Exception("IListModel::elementAt方法尚未实现");
	}
	
	public function lists($beginPos = 0, $endPos = 0x7FFFFFFF) { 
		throw new Exception("IListModel::lists方法尚未实现");
	}
	
	public function update($element, $position) {
		throw new Exception("IListModel::update方法尚未实现");
	}
	
	public function insert($element, $position = 0) {
		throw new Exception("IListModel::insert方法尚未实现");
	}
	
	public function delete($position) {
		throw new Exception("IListModel::delete方法尚未实现");
	}
	
	public function flush() { 
		//DO Nothing;
	}
}

```
`ListModel extends InterceptorContainer`，则`InterceptorContainer`这个类的类定义如下：
```
class InterceptorContainer implements IInterceptable {
	private $preInterceptors = array();
	private $postInterceptors = array();
	private $exceptionInterceptors = array();
			
	public function getPreInterceptors() {
		return $this->preInterceptors;
	}

	public function getPostInterceptors() {
		return $this->postInterceptors;
	}

	public function getExceptionInterceptors() {
		return $this->exceptionInterceptors;
	}
	
	public function setPreInterceptors($preInterceptors) {
		$this->preInterceptors = $preInterceptors;
	}

	public function setPostInterceptors($postInterceptors) {
		$this->postInterceptors = $postInterceptors;
	}
	
	public function setExceptionInterceptors($exceptionInterceptors) {
		$this->exceptionInterceptors = $exceptionInterceptors;
	}

	public function addPreInterceptor($interceptor) {
		array_push($this->preInterceptors, $interceptor);
	} 

	public function addPostInterceptor($interceptor) {
		array_push($this->postInterceptors, $interceptor);
	}

	public function addExceptionInterceptor($interceptor) {
		array_push($this->exceptionInterceptors, $interceptor);
	}

	public function firePreInterceptors($cmd, $params = array()) {
		foreach ($this->preInterceptors as $i) {
			$i->handle($cmd, $params);
		}
	} 

	public function firePostInterceptors($cmd, $params = array()) {
		foreach ($this->postInterceptors as $i) {
			$i->handle($cmd, $params);
		}
	} 
	
	public function fireExceptionInterceptors($cmd, $params = array(), Exception $e) {
		foreach ($this->exceptionInterceptors as $i) {
			$i->handleError($cmd, $params, $e);
		}
	} 
}
```
## SiteStore类继承
SiteStore被通过引用web/module/cert/ca/model/CertModel.php中引用srv/ica/store/SiteStore.php中类SiteStore的定义，其结构如下：
```
class SiteStore extends Store {
	
	function __construct() {
		$this->setBasePath(ICA_CFG_SITE_DIR);
	}
	
	public function generateCSR(Array $dn, $type = OPENSSL_KEYTYPE_RSA, $params = Array(), $allowDupName = false, $refKeyPath = null) {
		//检查DN项
		foreach ($dn as $name => $value) {
			switch ($name) {
			case "C":
			case "countryName":
			case "CN":
			case "commonName":
			case "ST":
			case "stateOrProvinceName":
			case "L":
			case "localityName":
			case "O":
			case "organizationName":
			case "OU":
			case "organizationalUnitName":
			case "emailAddress":
				break;
			default:
				throw new Exception("不合法的DN项：" . $name . "=" . $value);
			}
		}
		
		//检查必填项
		if (!array_key_exists("C", $dn) && !array_key_exists("countryName", $dn)) {
			$dn["C"] = "CN";
		}
		
		//计算证书hash
		$subjectHash = CertificateHelper::calcSubjectHash($dn);
		
		//检查同名的证书是否存在
		if (!$allowDupName) {
			//判断私钥文件是否存在，否则使用原先的私钥重新生成请求
			$file = $this->findKeyFileByHash($subjectHash);
			if ($file) {
				throw new Exception("该DN对应的私钥文件已存在，请先删除对应的证书和私钥");
			}
		}

		if ($refKeyPath) {
			//建立Key的符号链接
			$key = PrivateKeyFactory::createFromFile($refKeyPath);
			$keyPath = $this->getPrivateKeyPath($key, $subjectHash);
			OSUtils::symlink($refKeyPath, $keyPath);
		}
		else {
			//构造新私钥并保存
			$key = PrivateKeyFactory::createFromParams($type, $params);
			$keyPath = $this->getPrivateKeyPath($key, $subjectHash);
			$key->save($keyPath);
		}
		
		//构造证书请求
		$csrPath = $this->getCSRPath($key, $subjectHash);
		
		$csrContent = null;
		$key->generateCSR($dn, $csrContent, $csrPath);
		
		return $csrPath;
	}
```
### Store
	