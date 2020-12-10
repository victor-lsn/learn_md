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
2. 新建文件，并添加到暂存区
3. 提交文件到仓库
