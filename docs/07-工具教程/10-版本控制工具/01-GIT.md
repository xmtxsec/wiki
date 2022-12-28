
# 何为git
Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。<br />Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。<br />Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。


## Git 与 SVN 区别
Git 不仅仅是个版本控制系统，它也是个内容管理系统(CMS)，工作管理系统等。<br />如果你是一个具有使用 SVN 背景的人，你需要做一定的思想转换，来适应 Git 提供的一些概念和特征。

Git 与 SVN 区别点：

1. **Git 是分布式的，SVN 不是**：这是 Git 和其它非分布式的版本控制系统，例如 SVN，CVS 等，最核心的区别。
2. **Git 把内容按元数据方式存储，而 SVN 是按文件：**所有的资源控制系统都是把文件的元信息隐藏在一个类似 .svn、.cvs 等的文件夹里。
3. **Git 分支和 SVN 的分支不同：**分支在 SVN 中一点都不特别，其实它就是版本库中的另外一个目录。
4. **Git 没有一个全局的版本号，而 SVN 有：**目前为止这是跟 SVN 相比 Git 缺少的最大的一个特征。
5. **Git 的内容完整性要优于 SVN：**Git 的内容存储使用的是 SHA-1 哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

![image.png](./assets/1655795181482-d5890e00-ad12-42b5-843f-3225cfcd308b.png)


# git安装配置
Git 各平台安装包下载地址为：[http://git-scm.com/downloads](http://git-scm.com/downloads)


### 用户信息
配置个人的用户名称和电子邮件地址：
```
git config --global user.name "XXXX"
git config --global user.email XXX@XX.com
```

如果用了 **--global** 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个**特定的项目**中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。


### 查看配置信息
要检查已有的配置信息，可以使用 git config --list 命令


# 工作区与暂存区的区别
工作区：就是你在电脑上看到的目录，比如目录下git_test里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。<br />版本库：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

使用Git提交文件到版本库有两步：<br />第一步：是使用 git add 把文件添加进去，实际上就是把文件添加到暂存区。<br />第二步：使用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支上。


# git使用方法

## 创建版本库
版本库又名仓库，可以简单的理解为一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改，删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻还可以将文件”还原”。
```
mkdir git_test
```
![image.png](./assets/1655795239462-f9edfa3b-1018-4a61-9ab4-108635cf31b2.png)

进入目录，通过命令 git init 把这个目录变成git可以管理的仓库
```
cd git_test/
git init
```
![image.png](./assets/1655795248926-179b5723-2ac8-4ba0-a97c-2e7a5bc30f48.png)

目录下会多了一个.git的目录，这个目录是Git来跟踪管理版本


## 基本用法
新建一个记事本文件 test1.txt 内容为：test1


### 添加到缓存区
```
git add test1.txt
```
![image.png](./assets/1655795267144-77def0c3-c61b-424a-b972-0604f6097b97.png)


### 提交到仓库
```
git commit -m 'test1.txt提交'
```
![image.png](./assets/1655795276692-9b111652-a3ef-4ef6-aa65-339350bc6e81.png)


### 查看是否有文件未提交
```
git ststus
```
![image.png](./assets/1655795285463-69eee634-719d-4dc6-8eeb-80d71ebbc8b8.png)

说明没有任何文件未提交，修改test1.txt内容，比如下面添加一行test2内容
![image.png](./assets/1655795305142-6ef12f21-af15-4bb6-b678-73087517d899.png)

继续使用git status来查看下结果
![image.png](./assets/1655795312127-066aa601-5c36-42bc-9bcd-1707f7f44260.png)

命令告诉我们 test1.txt文件已被修改，但是未被提交的修改


### 查看文件已修改但未提交的内容
```
git diff test1.txt
```
![image.png](./assets/1655795322411-e6e0e880-af53-4d0b-b061-0d84590bf4a1.png)

可以看到，test1.txt文件内容从一行test1改成 二行 添加了一行test2内容。

知道了对test1.txt文件做了什么修改后，我们可以放心的提交到仓库了，提交修改和提交文件是一样的(第一步是git add 第二步是：git commit)。
```
git add test1.txt
git commit -m '增加内容test2'
```
![image.png](./assets/1655795339382-c3e442ce-b1b0-4665-9a04-fc324f4efeb2.png)

再次查看一下是否有未提交的内容
```
git ststus
```
![image.png](./assets/1655795348671-7ed6efa2-325b-4ffc-9e01-b88ead1c7777.png)


### 查看修改记录
继续对readme.txt文件进行修改，再增加一行：test3
![image.png](./assets/1655795358337-81b3d5c8-15a3-4788-b3db-42844f004e14.png)

提交
![image.png](./assets/1655795365689-f52bb46a-379a-47bb-a6e5-1518f0e6f4bd.png)

现在已经对test1.txt文件做了三次修改了，可以使用命令 git log 查看修改记录
```
git log
```
![image.png](./assets/1655795374059-2bc4a836-4425-45aa-8dff-ae2778efc413.png)

git log命令显示从最近到最远的显示日志，我们可以看到最近三次提交<br />如果嫌上面显示的信息太多的话，我们可以使用命令 git log --pretty=oneline
```
git log --pretty=oneline
```
![image.png](./assets/1655795384798-9ee210e3-c3df-480d-9760-f53d0e38b6e8.png)


### 版本回退

1. `git reset --hard HEAD^` 那么如果要回退到上上个版本只需把`HEAD^` 改成 `HEAD^^` 以此类推。
2. `git reset --hard HEAD~100` 回退到前100个版本。

回退到上一个版本
```
git reset --hard HEAD^
```
![image.png](./assets/1655795403696-33a0e057-59b5-442a-b1ab-12262d2b61c2.png)

查看历史记录
```
git log
```
![image.png](./assets/1655795413095-e7e137c6-1070-4837-8848-3ebc82d6f42d.png)

回退到最新的版本我们可以通过版本号回退，使用命令方法如下：

```
git reset --hard 版本号
```
但是现在的问题如何知道增加test3内容的版本号呢？

可以通过如下命令即可获取到版本号：
```
git reflog
```
![image.png](./assets/1655795452528-61f5a40a-5b1f-4812-b979-c25c54b2470b.png)

回退到版本3
```
git reset --hard de1e532
```
![image.png](./assets/1655795464763-36998bf4-bf80-4086-a667-4f1094dd15cf.png)


### 撤销修改
在test1.txt文件里面增加一行 内容为yesy4
![image.png](./assets/1655795474428-b905b755-b5d4-4a4f-9114-dcbd5e597e6c.png)
在未提交之前，发现添加yesy4内容有误，所以得马上恢复以前的版本，现在有如下几种方法可以做修改：

1. 如果知道要删掉那些内容的话，直接手动更改去掉那些需要的文件，然后add添加到暂存区，最后commit。
2. 可以按以前的方法直接恢复到上一个版本。使用 git reset --hard HEAD^。
3. 撤销修改。

首先在做撤销之前，我们可以先用 git status 查看下当前的状态
```
git status
```
![image.png](./assets/1655795493447-b337b821-7e77-449f-906f-f2485d1b7e77.png)

可以发现，Git会告诉你，git checkout -- file 可以丢弃工作区的修改
```
git checkout -- file1.txt
```
![image.png](./assets/1655795503615-5828a784-6aaa-4950-ae5a-ebb19981132b.png)

命令 git checkout -- test1.txt 意思就是，把test1.txt文件在工作区做的修改**全部撤销**，这里有2种情况，如下：

1. test1.txt自动修改后，还没有放到暂存区，使用撤销修改就回到和版本库一模一样的状态。
2. 另外一种是test1.txt已经放入暂存区了，接着又作了修改，撤销修改就回到添加暂存区后的状态。

**注意：命令git checkout -- readme.txt 中的 -- 很重要，如果没有 -- 的话，那么命令变成创建分支了。**


### 删除文件
假如现在版本库git_test目录添加一个文件test2.txt,然后提交。
```
git add test2.txt
git commit -m '添加文件test2.txt'
```
![image.png](./assets/1655795518505-a54ad717-bb72-4673-9972-c1ce258bd1ff.png)

直接在目录中删除test2.txt文件，但是在缓存区中依然存在，可以再执行commit命令提交掉。

只要没有commit之前，如果我想在版本库中恢复此文件，可以使用如下命令 git checkout -- test2.txt
```
git checkout -- test2.txt
```
![image.png](./assets/1655795530437-532873b5-3eaa-4801-9755-a78ffcafb27e.png)


### 总结
| 操作 | 代码 |
| --- | --- |
| 提交到缓存区 | git add 文件名 |
| 提交到仓库 | git commit -m '文件说明' |
| 查看未提交内容 | git status |
| 本地修改与缓存对比 | git diff 文件名 |
| 查看修改记录 | git log 、git reflog |
| 版本回退 | git reset -hard HEAD^  、git reset --hard HEAD~100 、git reset --hard 版本号 |
| 撤销修改 | git checkout -- 文件名 |



# 远程仓库

## 创建SSH Key
首先在本地创建ssh key
```
ssh-keygen -t rsa -C "your_email@youremail.com"
```

要求确认路径和输入密码，我们这使用默认的一路回车就行。
![image.png](./assets/1655795545664-956fc48f-b3b2-48d9-a562-212c27a5cac6.png)

成功的话会生成`.ssh`文件夹，进去，打开`id_rsa.pub`(id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人)，复制里面的`key`.
![image.png](./assets/1655795555598-f7aaff4e-a974-406a-a21b-aa3b8060a794.png)

到github上，进入 Account Settings（账户配置），左边选择SSH Keys，Add SSH Key,title随便填，粘贴在你电脑上生成的key。
![image.png](./assets/1655795562134-e92b1f08-4df2-4144-9168-d1e6b1b72ee7.png)

为了验证是否成功，在git bash下输入：
```
ssh -T git@github.com
```

如果是第一次的会提示是否continue，输入yes就会看到：You've successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。
![image.png](./assets/1655795570519-995b643e-4dca-4d13-bf19-455a60f1e80e.png)

把本地仓库传到github上去，在此之前还需要设置username和email，因为github每次commit都会记录他们。
```
git config --global user.name "your name"
git config --global user.email "your_email@youremail.com"
```
![image.png](./assets/1655795578318-6eca5875-60ca-416e-991d-ff604c2e365e.png)


## 添加远程库
登录github上，然后在右上角找到“new repo”创建一个新的仓库
![image.png](./assets/1655795585975-4ce971ca-eeaa-42f3-a621-339a09bb4d1b.png)

创建成功
![image.png](./assets/1655795594354-49866c8a-ab36-40d2-a1d2-41dd100ffcf0.png)

根据GitHub的提示，在本地的git_test仓库下运行命令，把本地库的内容推送到远程，使用 git push命令，实际上是把当前分支master推送到远程。
```
git remote add origin git@github.com:xmtxsec/git_test.git
git branch -M main
git push -u origin main
```
![image.png](./assets/1655795601923-08b749d9-1d27-4c06-90ed-0b9b0ca824fb.png)

由于远程库是空的，我们第一次推送master分支时，加上了 –u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。推送成功后，可以立刻在github页面中看到远程库的内容已经和本地一模一样了
![image.png](./assets/1655795610383-714a316e-ac36-49c0-9fd8-aa6dcfccfb00.png)

从现在起，只要本地作了提交，就可以通过如下命令：
```
git push origin master
```

把本地master分支的最新修改推送到github上了，现在你就拥有了真正的分布式版本库了。


## 克隆远程库
使用以下命令即可
```
git clone git@github.com:xmtxsec/git_test2.git
```

在github上创建仓库git_test2
![image.png](./assets/1655795633968-f35ebe86-f361-4630-8f6f-1008331f7542.png)

将git_test2克隆到本地
![image.png](./assets/1655795641625-dc809f8a-1b14-42fb-a41f-6c92816554db.png)


# 创建与合并分支

每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。


## 创建分支

创建dev分支，然后切换到dev分支上
```
git checkout -b dev
git branch
```
![image.png](./assets/1655795651877-568cf1a4-f225-4116-b7ce-559dbd9185cd.png)

```
git checkout 命令加上 –b参数表示创建并切换，相当于如下2条命令
git branch dev
git checkout dev
git branch查看分支，会列出所有的分支，当前分支前面会添加一个星号。
```

在test1.txt中添加内容test4
![image.png](./assets/1655795659074-b706ffd6-4925-4d1f-b87a-5c2c6998b6cb.png)

切换到主分支，查看test1.txt内容
```
git checkout main
```
![image.png](./assets/1655795666983-0703852a-2216-44c7-8be6-bc7d5a29311f.png)


## 合并分支
把dev分支上的内容合并到分支main上了，可以在main分支上，使用如下命令
```
 git merge dev
```
![image.png](./assets/1655795676482-bf992260-477f-4a55-8e66-cab5571c0fc0.png)

**git merge命令用于合并指定分支到当前分支上**，合并后，再查看readme.txt内容，可以看到，和dev分支最新提交的是完全一样的。


## 删除分支
合并完成后，我们可以接着删除dev分支了
```
git branch -d dev
git branch
```
![image.png](./assets/1655795686466-90e494e7-709d-4fed-8ccc-156a399cda1e.png)


## 命令总结
| 操作 | 代码 |
| --- | --- |
| 创建分支 | git branch name |
| 查看分支 | git branch |
| 切换分支 | git checkout name |
| 创建+切换分支 | git checkout -b name |
| 合并某分支到当前分支 | git merge name |
| 删除分支 | git branch -d name |



# 应用实例
在本地储存库创建test1内容为
```
这是测试test1
```

在本地储存库创建test2内容为
```
这是测试test2
```
![image.png](./assets/1655795701689-927d2a73-3934-4cc1-bfbe-8a8e43b90b1f.png)

上传到远程仓库
```
git add .
git commit -m '上传测试test1、test2'
git push origin main
```
![image.png](./assets/1655795709617-193cdbc4-a221-4640-a3ce-471d187d0383.png)

在远程仓库查看
![image.png](./assets/1655795716300-1632ce98-7072-45ec-96be-fbb220726f2f.png)

在远程仓库修改test1为
```
测试从远程更新test1
```
![image.png](./assets/1655795726579-88b254c0-1874-4c86-afa6-a89b2353dbe7.png)

从远程仓库更新到本地
```
git pull
```
![image.png](./assets/1655795735907-7356fe42-bb0d-4a47-ac0f-15d6e2d0dd3a.png)
![image.png](./assets/1655795742064-0cb5ad06-0b39-44d3-8c0a-0a1e7b7d9a5a.png)
