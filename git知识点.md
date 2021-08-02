#### git 基础

##### 中央仓库

![中央仓库](https://user-gold-cdn.xitu.io/2017/11/30/1600a9978ea4bec3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### 分布式VCS

分布式 VCS （Distributed VCS / DVCS）和中央式的区别在于，分布式 VCS 除了中央仓库之外，还有本地仓库：团队中每一个成员的机器上都有一份本地仓库。

![](https://user-gold-cdn.xitu.io/2017/11/30/1600a9a4a20c2e6e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### git 简单命令

git 练手仓库：[github 仓库](https://github.com/dongyeforever/ihappy.git)

```shell
git 简单指令汇总
git clone
git log
git status
git diff
git add
git commit
git push
```



- 把远程仓库取到本地

```shell
git clone https://github.com/dongyeforever/ihappy.git
```

查看本地目录，这个 `.git` 目录，就是你的**本地仓库（Local Repository）**，你的所有版本信息都会存在这里。而 `.git` 所在的这个根目录，称为 Git 的**工作目录（Working Directory）**

```shell
➜  ihappy git:(main) l
total 40
drwxr-xr-x   6 yu  staff   192B  2 19 17:45 .
drwxr-xr-x   5 yu  staff   160B  2 19 17:45 ..
drwxr-xr-x  12 yu  staff   384B  2 19 17:45 .git
-rw-r--r--   1 yu  staff   278B  2 19 17:45 .gitignore
-rw-r--r--   1 yu  staff    11K  2 19 17:45 LICENSE
-rw-r--r--   1 yu  staff    26B  2 19 17:45 README.md
```

- 查看提交记录

```shell
git log
```

```shell
commit 03dcf7490765baf0394d518a3c6b642e9c5f88b2 (HEAD -> main, origin/main, origin/HEAD)
Author: dongyeforever
Date:   Fri Feb 19 17:40:10 2021 +0800

    Initial commit
(END)
```

- 查看工作目录当前状态

```shell
 git status
```

- 添加到本地仓库

  文件添加前红色，添加后变成绿色。文件的状态从 “untracked”（未跟踪）变成了 “staged”（已暂存）。所谓的 staging area，是 `.git` 目录下一个叫做 `index` 的文件。你通过 `add` 指令暂存的内容，都会被写进这个文件里。

```shell
git add test.txt
git add .
```

- 提交到本地仓库

  将暂存区里的改动给提交到本地的版本库。

```shell
git commit
git commit -m "修改内容"
```

- 提交远程仓库

  多人并行开发时 git push 发生冲突，中央仓库已经被其他同事先一步 `push` 了的情况。先用 git pull  把远端仓库上的新内容取回到本地和本地合并，然后再把合并后的本地仓库向远端仓库推送。

```shell
git push
```

 ##### .gitignore 文件

忽略不想被管理的文件或目录。

```gradle
*.class
bin/
build/
local.properties
```

如果没有添加到 .gitignore 文件已经被添加到暂存区了：

```shell
git rm --cached filename
```

##### git 配置 

```shell
git config --list  // 查看配置
// 用户信息
git config --global user.name "name"
git config --global user.email mail@example.com
//远程仓库
git remote -v
git remote add upstream https://github.com/dongyeforever/ihappy.git // 增加remote
git remote remove upstream // 删除 remote
git branch --set-upstream-to=origin/feature  feature // 关联远程分支
```



#### git 进阶

- 引用

第一行的 `commit` 后面括号里的 `HEAD -> master, origin/master, origin/HEAD` ，是几个指向这个 `commit` 的引用。每一个 `commit` 都有一个它唯一的指定方式——它的 SHA-1 校验和（03dcf7490765baf0394d518a3c6b642e9c5f88b2），你可以使用`03dcf74`代替这个commit。

- HEAD

当前 `commit` 在哪里，`HEAD` 就在哪里，这是一个永远自动指向当前 `commit` 的引用，所以你永远可以用 `HEAD` 来操作当前 `commit`。

- branch

![分支](http://user-gold-cdn.xitu.io/2017/11/20/15fd779f983c81e7?w=568&h=306&f=gif&s=144193)

##### branch 创建、切换和删除

1.创建一个叫做 “feature” 的 `branch`

```shell
git branch feature
```

2.切换到 “feature”分支

```shell
git checkout feature
```

除此之外，你还可以用 `git checkout -b 名称` 来把上面两步操作合并执行。

```shell
git checkout -b feature
```

3.删除分支

```shell
git branch -d feature  // 出于安全考虑，没有被合并到 master 过的 branch 在删除时会失败
git branch -D feature  // 直接删除
```

##### git push

![push](http://user-gold-cdn.xitu.io/2017/11/29/160071880ab72f00?w=567&h=825&f=jpeg&s=47425)

```shell
// 提交 main 分支 commits
git push
// 切换到 feature 分支
git checkout feature
// 提交 feature 分支到远程仓库
git push [-u] origin feature
```

##### merge 合并 commits

`merge` 的意思是「合并」，它做的事也是合并：指定一个 `commit`，把它合并到当前的 `commit` 来。具体来讲，`merge` 做的事是：

**从目标 `commit` 和当前 `commit` （即 `HEAD` 所指向的 `commit`）分叉的位置起，把目标 `commit` 的路径上的所有 `commit` 的内容一并应用到当前 `commit`，然后自动生成一个新的 `commit`。**

```shell
git merge feature
```



![merge](http://user-gold-cdn.xitu.io/2017/11/21/15fddc2aad5a0279?w=640&h=454&f=gif&s=175263)

> git pull  等价于 git fetch + git merge

合并冲突

如果两个分支修改了同一部分内容，`merge` 的自动算法就搞不定了。这种情况 Git 称之为：冲突（Conflict）。

- 解决冲突
- 手动提交

```shell
git add xxx
git commit
```

 取消 merge

```shell
git merge --abort
```



##### Pull Request 工作流

这一节介绍了 Feature Branching 这种工作流。它的概念很简单：

1. 每个新功能都新建一个 `branch` 来写；
2. 写完以后，把代码分享给同事看；写的过程中，也可以分享给同事讨论。另外，借助 GitHub 等服务提供方的 Pull Request 功能，可以让代码分享变得更加方便；
3. 分支确定可以合并后，把分支合并到 `master` ，并删除分支。

这种工作流由于功能强大，而且概念和使用方式都很简单，所以很受欢迎。再加上 GitHub 等平台提供了 Pull Request 的支持，目前这种工作流是商业项目开发中最为流行的工作流。

##### git 查看修改记录

- git log 查看历史记录

```shell
git log --stat // 查看大致改动
git log --graph
```

- git show [commit-id] 查看任意一个 commit 修改
- git diff 查看未提交内容

##### 不喜欢 merge 的分叉？用 rebase 吧

`rebase` 指令，它可以改变 `commit` 序列的基础点。

![merge](http://user-gold-cdn.xitu.io/2017/11/21/15fdea7b6646a1f3?w=640&h=454&f=gif&s=175263)

![rebase](http://user-gold-cdn.xitu.io/2017/11/30/1600abd620a8e28c?w=698&h=518&f=gif&s=337134)

可能会出问题的 rebase：

```shell
git rebase feature // 在master上直接rebase
```

![rebase](http://user-gold-cdn.xitu.io/2017/12/2/16014bc64d4337f8?w=643&h=640&f=jpeg&s=58468)

不会出问题的 rebase 方式：

```shell
git checkout feature
git rebase main // 5、6 的分支基点从 2 换成了 4
git checkout main
git merge feature // 合并过来 feature 分支
```

> 所以，为了避免和远端仓库发生冲突，一般不要从 `master` 向其他 `branch` 执行 `rebase` 操作。而如果是 `master` 以外的 `branch` 之间的 `rebase`（比如 `branch1` 和 `branch2` 之间），就不必这么多费一步，直接 `rebase` 就好。

##### 刚刚提交的代码，发现写错了怎么办？

“amend” 是「修正」的意思。在提交时，如果加上 `--amend` 参数，Git 不会在当前 `commit` 上增加 `commit`，而是会把当前 `commit` 里的内容和暂存区（stageing area）里的内容合并起来后创建一个新的 `commit`，**用这个新的 `commit` 把当前 `commit` 替换掉**。所以 `commit --amend` 做的事就是它的字面意思：对最新一条 `commit` 进行修正。

```shell
git add test.txt
git commit --amend
```

##### 丢弃刚写的提交(最新)

```shell
git reset --hard HEAD^
git reset --hard commit-id

// 撤销某个提交
git rebase -i HEAD^^
```

##### push 后发现写错了

1.在自己 `branch` 上，把错误的 commit 修改或者删除，然后 `push -f`强制提交上去。

```shell
git push origin feature -f
```

2.出错的内容已经合并到 master 上

在 `revert` 完成之后，把新的 `commit` 再 `push` 上去。

```shell
git revert HEAD^
```

##### 紧急情况，需要紧急打包

当你手头有一件临时工作要做，需要把工作目录暂时清理干净，那么你可以：

```shell
git stash
```

工作目录的改动就被清空了，所有改动都被存了起来。

打包完后，切回你的分支，然后：

```shell
git stash pop
```

> 注意：没有被 track 的文件（即从来没有被 add 过的文件不会被 stash 起来，因为 Git 会忽略它们。如果想把这些文件也一起 stash，可以加上 `-u` 参数，它是 `--include-untracked` 的简写。就像这样：
>
> git stash -u

```shell
git stash list
```

##### Branch 删除了还想用，reflog

引用的 log，使用它可以查看 Git 仓库中的引用的移动记录。

```shell
git reflog
```

```shell
212b0ed (HEAD -> main) HEAD@{0}: checkout: moving from feature to main
34c6314 (origin/main, origin/HEAD, feature) HEAD@{1}: checkout: moving from 34c6314f89e11aa96632390f5209647bd894233a to feature
34c6314 (origin/main, origin/HEAD, feature) HEAD@{2}: checkout: moving from main to 34c6314
212b0ed (HEAD -> main) HEAD@{3}: commit: 增加文件
```
从图中可以看出，HEAD 的最后一次移动行为是「从 feature 移动到 main」。而在这之后，feature 就被删除了。所以它之前的那个 commit 就是 feature 被删除之前的位置了，也就是第二行的 34c6314。

所以现在就可以切换回 34c6314，然后重新创建 feature ：
```shell
git checkout 34c6314
git checkout -b feature
```

这样，你刚删除的` feature` 就找回来了。

##### tag

上线发版打 tag

```shell
git tag v1.0.0
```

##### cherry-pick 挑选 commit 合并进来 

```shell
git cherry-pick commit-id

//如果遇到冲突
git cherry-pick --continue
```



---

#### 实践

##### 1. 配置用户信息

```shell
git config --global user.name "name"
git config --global user.email mail@example.com
```

##### 2. git 命令

```shell
git clone https://xxxx  或者 ssh://xxxx  # 第一次 

git add .
// 提交到本地
git commit -m "commit 记录"
// 提交
git push # 第一次如果失败：  git push -u origin master
```

##### 3. ssh 免密配置

```shell
ssh-keygen -t rsa
```
> 建议 RSA 存储在`默认路径`，且`密码为空`直接回车。

把 `id_rsa.pub` 内容 添加到 SSH keys 里。设置路径：个人头像 - setting - ssh keys

##### 4.同一台电脑可以有2个git账号
修改配置文件`.ssh/config`。

```shell
  1 # douban
  2     Host github-fm.intra.douban.com
  3     HostName github-fm.intra.douban.com
  4     PreferredAuthentications publickey
  5     IdentityFile ~/.ssh/id_rsa_douban
  6
  7 # github
  8     Host github.com
  9     HostName github.com
 10     PreferredAuthentications publickey
 11     IdentityFile ~/.ssh/id_rsa
 ```
