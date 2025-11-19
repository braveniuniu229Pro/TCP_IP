# 常用工作流程
## 一.对于一个别人的仓库
先fork 这一步是完全复制到自己的远程仓库 这个远程分支一般就是叫upstream或是随便你起的名字
你fork到你远程仓库的仓库就是你可以随意提交修改的远程仓库，这个一般命名为origin 然后我们直接clone，这一步是把origin中的所有内容全部下载到本地。
git clone <你的远程仓库地址>
然后我们添加一个上游仓库upstream（就是相当于有两个远程仓库，一个是人家原本的你无法更改的，一个是你克隆的人家的可以修改的你的远程仓库）
git remote add upstream（这个名字随意起） <别人的远程仓库地址>
## 二.对于一个自己新建的仓库
直接在远程新建一个空仓库
在本地新建一个文件夹，并进行git初始化
然后设置远程分支为你刚才新建的空仓库
然后上传即可

# 常用操作命令
1. 查看所有分支
git branch -a
2. 查看远程分支
git branch -r
1. 查看追踪关系
git branch -vv
1. 设置当前分支的远程跟踪分支
git branch --set-upstream-to=origin/分支名 或直接git push -u origin 分支名
1. 删除远程跟踪分支
当前分支：git branch --unset-upstream
其他分支：git branch --unset-upstream 分支名
1. 删除分支本身
git branch -d 分支名（删除本地分支）
git push origin --delete 分支名（删除远程分支）
1. 查看远程仓库
git remote -v
1. 修改远程仓库名字
git remote rename 旧名字 新名字
1. 删除远程仓库
git remote remove 远程仓库名字
1.  添加远程仓库
git remote add 远程仓库名字 远程仓库地址
1.  拉取远程仓库的内容
git fetch 远程仓库名字 这一步相当于就是首先把远程仓库的提交对象都下载下来，然后下载所有分支的引用关系，但是对于本地仓库本身不会有任何影响。
git merge 远程仓库名字/分支名
或者直接
git pull
1.  清理已失效的远程分支引用
git fetch --prune 远程仓库名字
1.  reset的三种模式 
比如现在的提交记录是A-B-C-D-E HEAD指向E
- git reset --soft D 只移动HEAD指针 不会更改暂存区和工作区 相当于是只有本地仓库变成了D，其余两个都是E，直接commit又变回去了
- git reset --mixed D 这是默认模式 会移动HEAD指针和更改暂存区 不会更改工作区 相当于是本地仓库和暂存区都变成了D 工作区还是E 需要重新add到暂存区 然后commit又变回去了
- git reset --hard D 会移动HEAD指针 更改暂存区和工作区 相当于是本地仓库 暂存区 工作区都变成了D 注意这个操作会丢失工作区的修改

- 和checkout的区别
- checkout本质是用来切换head的，而分支指针不动
- 而reset是用来重置分支指针的，HEAD只是跟着移动
- checkout分支就是切换到分支 checkout版本就是把HEAD切换到某个版本

- 注意第一次设置远程分支的时候，在添加远程仓库后应该现fetch一下，然后才能更新远程仓库的分支信息，然后进行上游分支的绑定。
14. 不同的merge方式
git checkout B
git merge C
B：合并分支 C：被合并分支
- 正常merge：把C和B合成一个新提交M放在BC的后面，其中B分支的指针指向这个M，而C不动
- 其中如果C只是单纯的领先，那么不会产生新提交，就会移动B的指针到C
    有两个对应参数 --no-ff 这样的话就会强制产生一个新提交，即便可以快进
                  --ff-only 这样就只允许快进提交，其他的会被拒绝
    --squash 这样会产生一个新的提交，直接插入B的后面，但和C一点关系没有，即M只有唯一祖先B
git rebase C相当于是直接找到公共祖先，然后把C在公共祖先的后面的提交产生新的提交直接插到C的后面，然后用B指向，注意C也指向新的，老的被丢弃了。

对应的在远程分支和本地仓库分支的合并策略中：
首先只允许在本地领先时才能push，即只能快进指针
否则需要先pull
这里分为两种情况：
一：远程领先 ，直接pull然后本地快进到远程的版本
二：远程有领先有落后，需要merge
这时注意使用pull的时候你的合并分支是本地分支，被合并分支就是远程分支
因此就很容易理解了
普通的pull就是merge就是把远程的和本地的产生个新提交插在本地后面 然后你再push就可以了
而pull --rebase则是把本地超过远程的部分变成对应的新提交插到远程分支的后面，这样就变成了一个线性链，不用处理冲突，同样再push就可以了。

查看log的综合命令 --oneline是一行 --graph是图 --decorate是显示tag这些 --all是所有分支
git log --oneline --graph --decorate --all