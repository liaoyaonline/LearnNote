# 目录
# 前置条件：申请github帐号，以及安装github
&emsp;&emsp;1,github已经安装好了，如果没有请参考：[Ubuntu下配置和使用GitHub](https://www.linuxidc.com/Linux/2016-12/137911.htm)
# 设置缓存文件夹
* git fetch origin//用于查询新分支
* git checkout xxx//切换到新分支

    mkdir github//创建一个文件夹
    cd github//进入该文件夹
    git init//初始化本地仓库，以后你可把想上传文件移动到此文件夹
    ls -ah//检查该目录下所有文件，是否多了一个.git的隐藏文件夹
    git add ./ //添加你想要的文件进入缓存文件夹
    git status//查看缓存文件夹内容
    git commit -m "备注"//为这次提交备注
    git push -u origin master//第一次提交
    git push//上传
    git pull//拉取
    git remote -v //查看clone的地址 
# 将目标文件移缓存文件夹
&emsp;&emsp;如果你要上传的文件不在刚才新建的github文件夹里面先将该文件移动到github文件夹里面</br>
&emsp;&emsp;如果你已经将目标文件移入刚创建的github文件夹，请执行以步骤

# 查看文件状态
# 将文件上传到目标仓库

- git inint //初始化
- git add ./* //将所有文件加入缓存文件夹
- git status //查看缓存文件内容
- git commit -m "haha"//添加备注为haha
- git remote add origin git@github.com:upcAutoLang/Framework-for-NACIT2017.git//添加一个名为origin的远程仓库
- git push origin master//将内容提交到名为orgin的远程仓库