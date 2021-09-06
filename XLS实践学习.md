# XLS实践学习
## xsl:variable
### 定义和用法
`<xsl:variable>`元素用于声明局部或者全局的变量。如果作为顶层元素`top-level element`来声明，就是全局变量。如果在模板内声明，就是局部变量。一旦设置了变量的值，就无法改变或者修改该值！可以通过`<xsl:variable>`元素的内容或通过`select`属性，向变量添加值。
### 属性和语法
|属性|值|介绍|
|--|--|--|
|name|name|必需，规定变量的名称|
|select|expression|可选，定义变量的值|
```
<xsl:variable name="name" select="expression">
<!== Content:template -->
</xsl:variable>
```
### 实例
* 如果设置了`select`属性，`<xsl:variable>`元素就不能包含任何内容。如果`select`属性含有文字字符串，则必须给字符串加引号，下面两个例子给变量`"color"`赋值`"red"`:
```
<xsl:variable name="color" select="'red'"/>

<xsl:variable name="color" select='"red"'/>
```