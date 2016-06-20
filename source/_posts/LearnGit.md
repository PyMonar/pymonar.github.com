---
title: Learn Git
date: 2016-06-20 21:29:32
tags:
---

> 记录一些常用的git命令

### 基本配置

```
git config --global #全局配置

git config --list #配置清单

git config --global alias.co checkout #设置别名checkout

git help #帮助

```
---

### 基础

```
git init #初始化仓库

git clone [url] [another name] #从远程仓库克隆

git status #查看当前状态

git add [file path] #跟踪当前某文件，纳入版本仓库，add就是放入暂存区的意思，如果是文件夹则会递归纳入

git diff [--cached] #查看修改文件之后还未暂存的变化，加了参数--cached是查看暂存之后与版本库的变化

git commit [-m] [#] #提交到版本库所有暂存区域的更新，-a参数会跳过暂存，直接将已跟踪的所有文件直接提交到版本库

git rm [--cached] [file name] #移除某个文件的跟踪，并将其从文件目录删除，如果加--cached参数的话只是从版本库移除跟踪

git mv [file form] [file to] #文件重命名

git commit --amend #修改最后一次提交

git reset HEAD [file] #取消暂存的文件

git checkout -- [file] #取消修改的文件，回到之前的提交状态文件

```

> .gitignore 文件规则使用glob模式，所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。星号（`*`）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。

---

### 日志

```
git log #查看日志

git log -p -2 #-p显示文件差异，-2显示最近两次

git log --stat #显示摘要信息

git log --pretty=oneline/short/full/fuller #提供格式化输出方式

git log --pretty=format:"%h - %an, %ar : %s" #按照自定义格式输出log

git log --pretty=format:"%h %s" --graph #图形化展示

git log --since/until/after/before=2.weeks #特定时间的log

git log --author/committer

```

> git log 选项 说明

> -p 按补丁格式显示每个更新之间的差异。

> --stat 显示每次更新的文件修改统计信息。

> --shortstat 只显示 --stat 中最后的行数修改添加移除统计。

> --name-only 仅在提交信息后显示已修改的文件清单。

> --name-status 显示新增、修改、删除的文件清单。

> --abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。

> --relative-date 使用较短的相对时间显示（比如，“2 weeks ago”）。

> --graph 显示 ASCII 图形表示的分支合并历史。

> --pretty 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。

---

> format 选项 说明

> %H 提交对象（commit）的完整哈希字串

> %h 提交对象的简短哈希字串

> %T 树对象（tree）的完整哈希字串

> %t 树对象的简短哈希字串

> %P 父对象（parent）的完整哈希字串

> %p 父对象的简短哈希字串

> %an 作者（author）的名字

> %ae 作者的电子邮件地址

> %ad 作者修订日期（可以用 -date= 选项定制格式）

> %ar 作者修订日期，按多久以前的方式显示

> %cn 提交者(committer)的名字

> %ce 提交者的电子邮件地址

> %cd 提交日期

> %cr 提交日期，按多久以前的方式显示

> %s 提交说明

---

### 远程仓库

```
git remote [-v] #查看远程仓库

git remote add [short name] [url] #添加远程仓库

git fetch [remote name] #从远程仓库抓取更新，只抓取，并不合并

git push [remote name] [branch name] #向远程仓库推送更新，推送必须别人没有更新才可以，否则需要先取回本地合并

git remote show [remote name] #查看远程仓库详情

git remote rename [old name] [new name] #重命名远程仓库名

git remote rm [remote name] #移除远程仓库

```

---

### 标签

```

git tag # 查看现有标签

git tag -a [v1.0] -m [#] #新建带注释的标签

git show [v1.0] #查看某个版本信息

git tag [v1.1] #创建轻量级标签

git tag -a [v1.0] [hash] #给特定提交添加标签

git push [remote] [tag name]/[--tags] #推送标签

```
---

### 分支

```

git branch [branch name] #新建分支

git checkout [branch name] #切换分支

git checkout -b [branch name] #新建并切换到分支

git merge [branch name] #在当前分支上合并[branch name]分支

git branch -d [branch name] #删除分支

git branch [-v]/[-vv] #给出当前所有分支列表，*号代表当前所在分支

git branch [--merged]/[--no-merged] #查看那些分支合并进入了当前分支，即哪些分支是当前提交对象的直接上游

git fetch [origin] #获取服务器端的数据到本地，更新远程分支指向

git push [origin] [master] #向远程仓库推送自己的数据上去，更新远程分支的指向

git checkout -b [local branch name] [remote branch name] #本地新建分支与远程对应

git checkout --track [remote branch name] #本地开始跟踪远程分支

git push [origin]  :[branch name] #删除远程分支名

git rebase [origin/master] #将自己的修改衍合到主干分支上去

```
> git会维护一个HEAD的指针，指向当前分支的顶端。

> 切换分支最好保留一个干净的工作区域。

> 远程分支在本地无法移动，只是一个标记位。远程分支使用[origin]/[master]这种方式展现。
