title: Git 笔记
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2018-03-24 10:49:00
---
<>表示其中名称可修改<!--more-->

---
### 命令
---
创建版本库

1.选择一个合适的地方，创建一个空目录：
```
$ mkdir learngit
$ cd learngit
$ pwd
```
2.通过git init命令把这个目录变成Git可以管理的仓库：`$ git init`
3.在其中放入一个readme.txt文件
```
Git is a version control system.
Git is free software.
```
4.把一个文件放到Git仓库

- 4.1 把文件添加到仓库：`$ git add readme.txt`; 
- 4.2 把文件提交到仓库：`git commit -m "wrote a readme file" #-m后面输入的是本次提交的说明`
----

5.`git status`命令提示文件是否被修改过，随时掌握工作区的状态。

6.`git diff readme.txt`查看具体修改了什么内容 //之后还想提交继续`git add <file>` 和 `git commit` 两步

<font color="green">注意</font>：`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

7.`git log`命令查看历史记录 //后加 `--pretty=oneline参数` 简化显示结果

8.回退版本：`git reset --hard HEAD^` 注意其中`^`的数量会变动，如上上一个版本就是`HEAD^^`； 或者把`HEAD^`直接改为目标版本的commit id。

9.`git reflog`用来记录用户的每一次命令

10.提交后，用`git diff HEAD -- <filename>`命令可以查看工作区和版本库里面最新版本的区别

11.`git checkout -- file`可以丢弃工作区的修改
(让这个文件回到最近一次git commit或git add时的状态。)

12.（已经提交到版本库但后续删除了的文件）
删除版本库中的该文件：1. `git rm <filename>`；2. `git commit -m "balabala"`
根据版本库恢复该文件：`git checkout -- <filename>` （git checkout其实是用版本库里的版本替换工作区的版本）

13.添加远程库： 
1. 在Github上新建一个repo
2. <font color="red">把创建好的SSH公钥放入Github账户设置中，私钥通过`ssh-agent`、`ssh-add <私钥名称>`添加，注意这里是教程中没有提过的。</font>
3. 在本地想要推送的文件中：`git remote add <origin> git@github.com:<username>/<repo's name>.git`
4. 关联后，使用命令`git push -u <origin> master`第一次推送master分支的所有内容；
5. 之后`git push <origin> master`推送最新修改

14.克隆远程库
1. `git clone git@github.com:<username>/<repo's name>.git`

15.`git stash`保存当前工作现场；`git stash list`查看刚才存储工作现场的具体位置；`git stash pop`恢复工作现场

16.`git branch -D <name>` 强行删除一个没有合并过的分支

17.`git remote -v` 显示更详细的远程库信息

18.`git push origin <分支名字>`把该分支上的所有本地提交推送到远程库

19.在本地创建和远程分支对应的分支，使用`git checkout -b <branch-name> origin/<branch-name>`，本地和远程分支的名称最好一致；建立本地分支和远程分支的关联，使用`git branch --set-upstream <branch-name> origin/<branch-name>`；

20.（在Git中打标签）
1. 切换到需要打标签的分支上；
2. `git tag <name>`打标签
3. `git tag`查看标签
（给特定的commit打标签）
1. `git tag <name> <commit-id>`（commit-id可以用`git log --pretty=oneline --abbrev-commit`查看）

21.`git show <tagname>`查看标签信息

22.`git tag -a v0.1 -m "version 0.1 released" 3628164` 创建带有说明的标签，用-a指定标签名，-m指定说明文字

23.`git tag -s <tagname> -m "blablabla..."`可以用PGP签名标签

24.（操作标签）
1. `git push origin <tagname>`可以推送一个本地标签
2. `git push origin --tags`可以推送全部未推送过的本地标签
3. `git tag -d <tagname>`可以删除一个本地标签
4. `git push origin :refs/tags/<tagname>`可以删除一个远程标签（注意若想删除的标签已推送远程，需先删除本地标签再删除远程标签）

25.`git check-ignore -v <App>.<class>`找出该类型文件关联了.gitignore哪个规则

26.`git config --global alias.<st> <status>` 设置st就表示status（设置别名的配置文件放在`.git/config`文件中 可以用cat/vi命令查看/修改）


### 概念
1. 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。![](https://wx4.sinaimg.cn/mw690/b11f7046gy1fpn11w0pvlj20cq06iaa8.jpg)

2. （回退）
场景1：想直接丢弃工作区的修改时：`git checkout -- file`。
场景2：改乱了工作区并添加到了暂存区时，想丢弃修改：第一步用命令`git reset HEAD file`，第二步见场景1。
场景3：已经提交了不合适的修改到版本库时想要撤销：`git reset --hard HEAD^`

3. [创建SSH key](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

4. Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

5. （创建合并分支）
master分支即主分支。master指向最新的提交，HEAD确定当前分支：![](https://wx2.sinaimg.cn/mw1024/b11f7046gy1fpo2g6iy0tj20br04fdfp.jpg)
1.创建dev分支并切换：`git checkout -b dev`（相当于`git branch dev`和`git checkout dev`）此时状态：![](https://wx4.sinaimg.cn/mw1024/b11f7046gy1fpo2g6kccbj20a706hwee.jpg)
2.`git branch`查看当前分支(当前分支前面会标一个*号)
3.（已在工作区文件上作出修改）在dev分支上正常提交：`git add <filename>`、`git commit -m "balabala"`此时状态：![](https://wx1.sinaimg.cn/mw1024/b11f7046gy1fpo2g6iu22j20dq06ha9z.jpg)
4.此时切换到master分支上`git checkout master`，把dev分支的工作成果合并到master分支上：`git merge dev`。可以发现文件内容保持一致。此时状态：![](https://wx1.sinaimg.cn/mw1024/b11f7046gy1fpo2g6jxeqj20br0663yf.jpg)
5.合并完成后，就可以删除dev分支：`git branch -d dev`

6. （解决分支之间的冲突）
master分支和自建的feature1分支各自都分别有新的提交，此时可以在master分支上把文件修改成真正想提交的内容，然后add+commit，如图![](https://wx2.sinaimg.cn/mw690/b11f7046gy1fpo37i7u1gj20fb07kmx0.jpg)
最后删除自建分支就可以了
（`git log --graph --pretty=oneline --abbrev-commit`命令可以看到分支合并图）

7. 通常合并分支时Git会用Fast forward模式，删除分支后会丢掉分支信息。如果要强制禁用Fast forward模式，从分支历史上就可以看出分支信息，则使用命令（示例在master分支上合并自建分支dev的内容）：`git merge --no-ff -m "merge with no-ff" dev` 目前状态如图：![](http://ww3.sinaimg.cn/large/0060lm7Tly1fpo3yproy4j30dc075web.jpg)

8. 多人协作的工作模式通常是这样：
1.首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2.如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`（抓取冲突）试图合并；
3.如果合并有冲突，则解决冲突，并在本地提交；没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！
4.如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream <branch-name> origin/<branch-name>`。

9. tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起

10. 忽略某些文件时，需要编写.gitignore；.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理；`-f`强制添加已忽略格式类型的文件：`git add -f <App>.<class>`




