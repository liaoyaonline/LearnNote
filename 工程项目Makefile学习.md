# 工程项目Makefile学习
## 项目工程文件夹
* bin //可执行文件

* src//源代码以及Makefile

* lib //库文件
## MakeFile编写
其流程就是将每个依赖进行编译，然后将所有依赖生成的`.o`文件进行编译输出可执行文件。做好这个后，对其中的文件采用宏定义的方式进行封装
## MakeFile编程实例
```
#编译客户端小程序
CC = gcc
#INCLUDES = ../lib/
TARGET = test
OBJS = main.o cClient.o cJSON.o
all:$(OBJS)
	$(CC) -o $(TARGET) main.o cClient.o cJSON.o -lm -lcrypto
main.o:
	gcc -c main.c
cClient.o:
	gcc -c cClient.c
cJSON.o:
	gcc -c cJSON.c
.PHONY:clean
clean:
	rm -f main.o cClient.o cJSON.o
```