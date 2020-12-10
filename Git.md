# Git

## 一次性配置

~~~shell
git config --gloable user.name "victor-lsn"     设置用户名
git config --gloable user.email VictorLSN@163.com    设置邮箱地址
git config --list    查看所有配置信息
~~~

## 三种状态与工作模式

| 状态                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| 已提交（committed） | 表示数据已经安全的保存在本地数据库中                         |
| 已修改（modified）  | 表示修改了文件，还没保存到数据库中                           |
| 已暂存（staged）    | 表示一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中 |

针对三种状态需要了解的三个工作区域：工作区、暂存区、Git仓库

| 分类    | 描述                                                      |
| ------- | --------------------------------------------------------- |
| 工作区  | 简单理解为自己本地创建的目录                              |
| 暂存区  | Git会自己创建的第一个分支master，以及指向master的指针HEAD |
| Git仓库 | 工作区有一个隐藏的目录.git，不算是工作区，而是Git的版本库 |

Git工作流程描述：

1. 在工作区中修改文件
2. 对修改的文件进行快照，添加到暂存区
3. 提交更新，将保存在暂存区的文件快照永久转储到Git仓库

## 流程

1. 初始本地初始化仓库

   ~~~shell
    git init
   ~~~

2. 新建文件，并添加到暂存区

   ~~~shell
   git add Git.md
   
   git status           查看状态
   ~~~

   ~~~shell
   出现以下信息
   On branch master
   Your branch is up to date with 'origin/master'.
   
   Changes to be committed:
     (use "git restore --staged <file>..." to unstage)   使用这条命令清空缓存区
           new file:   Git.md
   ~~~

3. 提交文件到仓库

   ~~~shell 
   git commit -m 'Git的基本使用'
    
   git log   查看日志
   git log -5 --pretty=oneline    显示5行日志，并每一行一个
   ~~~

## 时光穿梭

~~~shell
git reset HEAD          撤销暂存

git reset --hard HEAD^   回退到上一个提交的版本，可以任意加^，意为上的几个版本
git reset --hard HEAD~1  回退一个版本，后面数字为回退的几个版本
git reset --hard 唯一标识符   返回唯一标识符的版本

git reflog     显示执行的命令，并含有唯一标识符
~~~

对于已提交的文件如果在本地误删，可以从提交的下载回来

~~~shell
git checkout -- test.txt

git rm test.txt    可以同时删除本地目录中的文件和本地仓库中的
~~~

##  远程仓库

~~~shell
git clone 地址    克隆到本地
~~~

**推送到远程仓库**

1. 初始化本地仓库
2. 添加文件到暂存区
3. 提交本地库
4. 本地库与远程库绑定
5. 推送