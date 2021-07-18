# linux常用命令备忘
-  du -sh * //查看文件中大小

- fdisk -l //查看硬盘各个表的情况
- locate kcg//寻找名字包含kcg的文件

- df -TH //查看已经挂载的表的情况

- mkfs -t ext4 -c /dev/sdb1//将指定硬盘格式化，必须先卸载

- blkid //查看硬盘的UUID

- mount /dev/sdb1 /mnt //将/dev/sdb1分区挂载到/mnt文件夹下面	

- fdisk /dev/sdb //创建sdb里面的新分区

- /etc/fstab //自动挂载文件

- find / -name "a.c"//寻找根目录中名为a.c的文件
find /home/liaoya/github/novel-plus/ -name '*' | xargs grep '排行' > log //查找该目录下所有包含排行字符的文件

- unrar x etc.rar //将etc.rar按照路径解压
- unrar e -p etc.rar //解压带密码的rar
- 7za x 111.7z -p123456//解压带密码的7z
- unzip -O gbk 2541.txt.zip//解压带有GBK模式的zip，可以省略解压正常模式zip
- gedit --encoding GBK 马恩的日常.txt//选择使用GBK解码模式打开马恩的日常.txt
- sudo tar -jxvf filename.tar.bz2 -C FilePath//将文件filename解压到FilePath里面
- tar -zxvf studio.tar.gz//解压studio.tar.gz
- sudo aria2c --conf-path=/etc/aria2/aria2.conf //启动aria2

- chmod 777 filename.js //设置文件权限为所有用户读写操作
- sudo chmod 777 * -R eladmin//设置文件夹eladmin内的所有文件读写操作

- kill -9 PID//杀死PID为PID的进程

- awk '{$1="";print $0}' Treedata.txt//删除Treedata.txt的第一列

- %s/\r//g //替换所有\r符号为无
- :%s/ //g//去除空格；
- :%s/\n//g//去除换行
- %s/s/b/gc//将所有s替换成b，且手确认

- awk '{for(i=2;i<=NF;++i) printf $i "\t";printf "\n"}' test.txt//打印除了第一列的其他列

- dot -Tpdf treeone.dot -o treeone.pdf//将treeone.dot转化为图像

- lm //在引用math.h库时，需要在编译命令后面加-lm

- tree -L 1//将当前目录的文件用树状图表示出来

- trans -shell -brief//使用trans进行翻译

- sudo mount -t cifs -o username=liaoya,password=123 //192.168.122.67/Downloadsall /data/tmp//将某ip地址的共享文件夹(windows10)挂载到某个文件夹(linux)下面，目的是方便传递文件

- sudo mount -a//让挂载文件/etc/fstab文件生效

-  sudo apt-get --purge remove electron-ssr//卸载名为electron-ssr的程序星期四, 13. 五月 2021 03:41下午 
