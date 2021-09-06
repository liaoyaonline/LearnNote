# linuxC备忘
## 对于MD5加密解密类的
编译时需要在后面添加-lcrypto,因为MD5的库文件在crypto文件夹里面
> gcc main2.cpp -lcrypto

使用gdb测试时需要在编译时前面加g

> gcc -g base64test.c -lcrypto -o base64
```
gcc -c cClient.c
gcc -c cJSON.c
gcc -c mainreboot.c

gcc -o test cClient.o cJSON.o mainreboot.o -g -lm -lcrypto//编译多依赖源文件-g是用于生成可诊断
```
* set follow-fork-mode child//gdb断点测试时，对于fork生成子进程可能诊断不了，此命令为了能够诊断子进程