# Mysql命令与函数
0750
0620
## 命令
* 进入`mysql`
```
sudo su
mysql -uroot -p123//其中root是账号，123是密码
```
* 显示数据库
```
show databases;
```
* 新建数据库
```
create database books;//新建一个名为books的数据库
```
* 删除数据库
```
drop database books;//删除名为books的数据库
```
* 进入数据库
```
use books;//进入名为books的数据库
```
* 创建数据库中的表
```
CREATE TABLE IF NOT EXISTS `ing`(
   `BooksId` varchar(100) NOT NULL COMMENT '书本id和书单id',
   PRIMARY KEY ( `BooksId` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
```
CREATE TABLE IF NOT EXISTS `BookDone`(
   `BookId` varchar(100) NOT NULL COMMENT '书本id和书单id',
   `BookName` varchar(100) NOT NULL COMMENT '小说名',
   `BookAuthor` varchar(100) NOT NULL COMMENT '小说作者',
   `BookWordCount` varchar(100) NOT NULL COMMENT '小说字数',
   `Bookstatus` varchar(100) NOT NULL COMMENT '小说更新状态',
    `BookUpdata` varchar(100) NOT NULL COMMENT '小说最近更新时间',
    `BookScore` float(5,2) DEFAULT NULL COMMENT '小说评分',
    `BookScoreCount` int(11) DEFAULT NULL COMMENT '小说评分人数',
    `OneStarRate` float(5,2) DEFAULT NULL COMMENT '一星评分占比',
    `TwoStarRate` float(5,2) DEFAULT NULL COMMENT '二星评分占比',
    `ThreeStarRate` float(5,2) DEFAULT NULL COMMENT '三星评分占比',
    `FourStarRate` float(5,2) DEFAULT NULL COMMENT '四星评分占比',
    `FiveStarRate` float(5,2) DEFAULT NULL COMMENT '五星评分占比',
    `BookClass` varchar(1000) NOT NULL COMMENT '小说类型偏向',
    `AddListCount` int(11) DEFAULT NULL COMMENT '加入书单数目',
    `BookIntro` varchar(20000) NOT NULL COMMENT '小说介绍',
   PRIMARY KEY ( `BookId` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
```
CREATE TABLE `BooklistDone` (
  `BooklistId` varchar(100) NOT NULL COMMENT '书单Id',
  `BooklistName` varchar(100) DEFAULT NULL COMMENT '书单名',
  `BooklistAuthor` varchar(100) DEFAULT NULL COMMENT '书单作者',
  `BooklistViews` varchar(100) DEFAULT NULL COMMENT '书单观看人数',
  `BooklistCount` int(11) DEFAULT NULL COMMENT '书单内小说数目',
  `BooklistKeep` int(11) DEFAULT NULL COMMENT '收藏本书单人数',
  `BookType` varchar(1000) DEFAULT NULL COMMENT '书单内小说类型',
  `BooklistUpdata` varchar(100) DEFAULT NULL COMMENT '书单最近更新时间',
  `BooklistIntro` varchar(20000) DEFAULT NULL COMMENT '书单介绍',
  PRIMARY KEY (`BooklistId`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
上述是建立一个名为`ing`的表，设定了键值对，选定了主键。

* 在表中插入数据
```
INSERT INTO ing (BooksId) VALUES('172856');
```
* 查询数据
	* 查询数据表中的所有记录
	```
	select * from ing;//返回ing中的所有记录
	```
	* 查询数据表中的特定数据
	```
	SELECT * from ing WHERE BooksId='172856';//返回数据表中BooksId为172856的记录
	```
*  显示表结构
``` 
 SHOW CREATE TABLE ing \G;//显示数据表ing的结构
```
* 删除数据表
```
DROP TABLE BookDone;
```