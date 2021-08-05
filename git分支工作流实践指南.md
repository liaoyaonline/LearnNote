# git分支工作流实践指南
## 常用命令
> git branche branchName

创建新分支branchName
> git checkout branchName

切换到分支branchName

> git fetch origin
git checkout -b "1-" "origin/1-"

获取并查看此合并请求的分支
> git fetch origin
git checkout "master"
git merge --no-ff "1-"


合并分支并解决出现的任何冲突
> git push origin "master"

将合并结果推送到gitlab
## 主要分支介绍

主要分支是永久的分支


> 分支名: develop

分支用途:用于开发的分支，总是保持最新的开发进度。

> 分支名: master

分支用途:用于发布的分支。总是保持可发布的状态。

> 分支命名: stable/<版本号X.Y.x>(见版本号命名规范)

分支用途：同时维护多个版本

生命周期： 从master分支分出，完成功能后_不合并_到其他分支

> 分支命名: custom/<定制功能名称>

分支用途
