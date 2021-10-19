# linuxC备忘
## 头文件编译流程
* 预处理：用于将所有`#include`头文件及`#define`等宏定义替换成真正的内容，预处理后的得到的仍然是文本文件，但体积会大。

>1.将头文件中的内容（源文件之外的文件）插入到源文件中


> 2.进行了宏替换的过程，定义和替换了由`#define`指令定义的符号

>3.删除注释的过程，注释不会带到编译阶段

>4.条件编译

* 编译：将预处理之后的程序转换成特定汇编代码的过程 ，这里的编译不是指从源文件到二进制程序的全过程
* 汇编：汇编过程将上一步的汇编代码转换成机器码，这一步产生的文件叫目标文件，是二进制格式。
* 链接：链接过程将多个目标文件以及所需的库文件（`.so`等）链接成最终的可执行文件
## 对于MD5加密解密类的
编译时需要在后面添加-lcrypto,因为MD5的库文件在crypto文件夹里面

* gcc main2.cpp -lcrypto

* gcc -c -I./include ./src/regexbook4.c //-I指定头文件所在目录


使用gdb测试时需要在编译时前面加g

> gcc -g base64test.c -lcrypto -o base64
```
gcc -c cClient.c
gcc -c cJSON.c
gcc -c mainreboot.c

gcc -o test cClient.o cJSON.o mainreboot.o -g -lm -lcrypto//编译多依赖源文件-g是用于生成可诊断
```
* set follow-fork-mode child//gdb断点测试时，对于fork生成子进程可能诊断不了，此命令为了能够诊断子进程