# 编写MakeFile文件
Makefile是用于大型项目编译管理文件，他会根据makefile里面的内容自动执行编译。
## 关键词说明
> @ //在命令前面表示不要输出该命令本身的输出
## 实例
```
# 最简单的Makefile
all:hello hello2
	@gcc helo.c -o helo

hello:ready
	@echo "hello Makefile"

hello2:
	@echo "hello daiqf"

ready:
	@echo "I'm ready"
```
hello,hello2是执行all的依赖，然后hello,hello2是生成该依赖的动作