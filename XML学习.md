# XML学习
## 介绍
`XML`是被设计用来传输和存储数据结构的。`XML`语言没有预定义的标签。`XML`是独立于软件和硬件的信息传输工具。
## 语法
```
<bookstore>
    <book category="COOKING">
        <title lang="en">Everyday Italian</title>
        <author>Giada De Laurentiis</author>
        <year>2005</year>
        <price>30.00</price>
    </book>
    <book category="CHILDREN">
        <title lang="en">Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="WEB">
        <title lang="en">Learning XML</title>
        <author>Erik T. Ray</author>
        <year>2003</year>
        <price>39.95</price>
    </book>
</bookstore>
```
以上中`bookstore`是根结点，必须存在，`book`是子节点，`category`和`lang`是属性，`titele`,`author`,`year`,`price`是元素。