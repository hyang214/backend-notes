# Git的存储结构

## 初始化

首先使用```git init```初始化一个git项目，可以得到如下目录结构：
```
+ COMMIT_EDITMSG # 最近一次commit的消息
+ HEAD
+ config
+ description
+ hooks
+ info
+ objects
+ refs # 存储git引用
	+ refs/heads/name # 存储分支名字，以及相应的commit
	+ refs/tags/name # 存储tag名字，以及相应的commit
	+ refs/remotes/name # 存储远程分支，以及相应的commit
	+ refs/heads/name
```

其中：
| 文件 | 含义  | 备注  |
| --- | --- | --- |
| HEAD | 本地仓库 当前引用 | 初始化后的值是：ref: refs/heads/master |
| config | 当前git的配置  |   |
| description | 项目描述 |  |
| hooks | 客户端钩子脚本文件夹，存储全部本地的钩子脚本 | https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks |
| info |  |  |
| objects | 仓库本地，包括各种对象 |  |
| refs | 存储 |  |

## objects

通过tree命令查询数据```tree objects```

```git cat-file [-t] [-p]， -t可以查看object的类型，-p可以查看object储存的具体内容```

objects中存储的文件类型有：

+ blob类型
	+ 存储的文件本身
	+ git每次改动，都是新增一个二进制文件，而不是存储的增量

```
aa # 存储的内容
```

+ tree类型：
	+ 存储的是一个文件的目录结构
	+ blob的是一个具体的文件
	+ tree是另外一个目录结构

```
100644 blob e61ef7b965e17c62ca23b6ff5f0aaf09586e10e9	aa.txt
040000 tree 5cb3946f40fc478c335c511e44886b77f0a9dad8	b
```



+ commit类型
	+ 存储的是每次commit的信息
	+ 其中 tree：指向的是tree文件类型，即一次树结构的ref
	+ parent：指向上次的tree的ref
	+ 剩下就是提交相关的信息
```
tree e329540afb743b677d611b591264b302fc7411a5
parent 2e44036fe99529ff8cf399c76f2dcc0216b4aae3
author Hao YANG <yanghaoasian@outlook.com> 1600152611 +0800
committer Hao YANG <yanghaoasian@outlook.com> 1600152611 +0800

bb
```

所以平时git的存储结构是：
              commit
             /      \
           tree     blob
          /    \
         blob  blob


## refs

存储分支信息，指向的是一个commit

## 区域


+ 工作目录 （ working directory ）：操作系统上的文件，所有代码开发编辑都在这上面完成。
+ 索引（ index or staging area ）：可以理解为一个暂存区域，这里面的代码会在下一次commit被提交到Git仓库。
+ Git仓库（ git repository ）：由Git object记录着每一次提交的快照，以及链式结构记录的提交变更历史。

## Reference

+ [这才是真正的Git——Git内部原理揭秘！](https://zhuanlan.zhihu.com/p/96631135?utm_source=wechat_session&utm_medium=social&utm_oi=29393313333248)











