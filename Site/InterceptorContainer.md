# InterceptorContainer
## 源码解析
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
## 总结分析