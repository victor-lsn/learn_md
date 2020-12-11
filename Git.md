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

   ~~~shell
   git remote add origin https://github.com/victor-lsn/learn_md.git
   ~~~

5. 推送

   ~~~shell
   git push -u origin master
   ~~~



**SSH推送**

上方为Https方式，比较简单，使用SSH方式是Git推荐的方式，时间与响应效率都更高，下方进行配置

1. 使用Git客户端生成SSH公钥  

   ~~~shell
   ssh-keygen -t rsa -C "VictorLSN@163.com邮箱"
   ~~~

2. 下方找到生成public key的保存路径，使用记事本打开路径中的.pub文件并全选复制

3. 去github官网用户中的setting中，找到SSH and GPC keys创建一个SSH key，并把公钥粘贴进去

4. 在第四步中修改地址为SSH地址，其余操作不变



## 分支操作

本地仓库操作

| 命令                                     | 描述                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| git checkout branch                      | 切换到指定分支                                               |
| git checkout -b new_branch               | 新建分支并切换到新建分支                                     |
| git branch -d branch                     | 删除指定分支                                                 |
| git branch                               | 查看所有分支，并且*号标记当前所在分支                        |
| git merge branch                         | 合并分支（在主干上使用）                                     |
| git branch -m \| -M oldbranch new branch | 重命名分支，如果newbranch名字分支已经存在，则需要使用-M强制命名，否则，使用-m进行重命名 |

远程仓库Push与Pull操作

| 命令                                              | 描述                             |
| ------------------------------------------------- | -------------------------------- |
| git branch -a                                     | 查看本地与远程分支               |
| git push origin branch_name                       | 推送本地分支到远程               |
| git push origin :remote_branch                    | 删除远程分支（本地分支还在保留） |
| git checkout -b local_branch origin/remote_branch | 拉取远程指定分支并在本地创建分支 |
| git fetch                                         | 读取远程仓库的最新状态           |

操作同一文件会造成冲突，本地冲突解决冲突行即可；避免远程冲突，每次都使用命令git pull去远程仓库拉取最新的，然后解决冲突行



## 标签管理

| 命令                                | 描述                             |
| ----------------------------------- | -------------------------------- |
| git tag tag_name                    | 新建标签 默认为HEAD              |
| git tag -a tag_name -m 'xxx'        | 添加标签并指定标签描述信息       |
| git tag                             | 查看所有标签                     |
| git tag -d tag_name                 | 删除一个本地标签                 |
| git push origin tag_name            | 推送本地标签到远程               |
| git push origin --tags              | 推送全部未推送过的本地标签到远程 |
| git push origin :refs/tags/tag_name | 删除一个远程标签                 |

 

## 整合IDEA

