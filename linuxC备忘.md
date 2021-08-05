# linuxC备忘
## 对于MD5加密解密类的
编译时需要在后面添加-lcrypto,因为MD5的库文件在crypto文件夹里面
> gcc main2.cpp -lcrypto

使用gdb测试时需要在编译时前面加g

> gcc -g base64test.c -lcrypto -o base64