# git 问题记录

## garbage at end of loose object / object ... is corrupted

+ 时间：2019.04.10
+ 场景：写了一天的代码，涉及6次commit，新增二十个文件，改动几十个文件。晚上走之前push的时候，报错，发现无法push，当时的心情真是 哔了POI 的感觉。错误如下：
    ```
    Counting objects: 12, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (6/6), done.
    Writing objects: 100% (12/12), 1.03 KiB | 1.03 MiB/s, done.
    Total 12 (delta 2), reused 0 (delta 0)
    remote: error: garbage at end of loose object '7b8415c9e3a00366d8f50534475fabc6e6d9793b'
    remote: fatal: loose object 7b8415c9e3a00366d8f50534475fabc6e6d9793b (stored in /ceph_fs/git_repositories/group_name/repository_name.git/objects/7b/8415c9e3a00366d8f50534475fabc6e6d9793b) is corrupt
    To https://git_hostname/group_name/repository_name.git
     ! [remote rejected] report -> report (missing necessary objects)
    error: failed to push some refs to 'https://git_hostname/group_name/repository_name.git'
    ```
+ 解决：
1. 首先按照关键字 去 Google 和 百度 找解决方案，得到了一些命令，一顿猛操作，但是未解决。发现问题并不简单，开始细心看问题
1. 找到第一个解决方案，很悲观，但是能用，至少保证最新的代码还在
    ```
    rm -fr .git
    git init
    git remote add origin [your-git-remote-url]
    git fetch
    git reset --mixed origin/master
    git branch --set-upstream-to=origin/master master  
    ```
    本质就新建一个git文件，把整个项目的历史记录全部清除了。但是由于同时有多个分支依次等待测试，这个方案不能用，放弃。
1. 由于只有一个 object缺失，开始尝试是否能修复这个文件，然而一顿操作，文件未修复，放弃这个方案
1. 这个时候还发现，直接从远处clone repository，也会提示相同的错误，发现错误应该已经在remote了，估计应该是今天新的分支上的代码有问题；
1. 在备份整个项目后，直接强行删除了remote的有问题分支，再次尝试clone，成功；问题定位，错误是下午提交的代码导致的；
1. 找到几个git命令，分析错误的文件
    ```
    git cat-file -t c91ce852821d32e38jed7ddd04c93066e3c561ea
    ## 输出 tree
    ```
    发现是个tree文件，然后
    ```
    git ls-tree c91ce852821d32e38jed7ddd04c93066e3c561ea
    # 输出 一堆<blob hash 文件名>
    ```
确定是某个文件夹里面的代码有问题
1. 从最接近的分支checkout -B一个新的分支出来，然后手动把新的代码复制过来，add->commit->push，依旧报错！！！，看来是只要有这些问题就会有问题？？？很神奇。
1. git reset --hard，回滚一波，然后把刚才 git ls-tree 之外的文件手动复制过来，add->commit->push，成功！看来是，这些问题有问题。
1. 在有问题的文件对应的目录里面新建一个问题，add->commit->push，成功，说明不是路径的问题
1. 选择一个文件，改动了一点点，add->commit->push，成功，怀疑是这些文件的hash值已经记录，且导致问题
1. 手动把这些问题全部改一点点，add->commit->push，成功
1. 修复完成

+ 疑惑：已经不是很明白发生了什么，为什么会出错，以及解决的原因。解决的思路最后是在上次的分支的基础上，将新的代码重新apply上去，然后提交，但是为什么文件直接覆盖会报错，但是改动一点点就可以了，不能理解。

+ Reference:
	1. [Git: “Corrupt loose object”
](https://stackoverflow.com/questions/4254389/git-corrupt-loose-object/40098509)
	1. [Corrupted git tree?](https://stackoverflow.com/questions/4213598/corrupted-git-tree)