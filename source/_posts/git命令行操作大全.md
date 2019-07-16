---
title: git命令行操作大全
date: 2018-08-26 23:15:43
tags: [git]
---

&emsp;&emsp;以前，我算是一个git工具爱好者，像git gui，TortoiseGit这两个工具，无论哪一个都用得得心应手，感觉确实比直接使用git命令行要方便得多，主要还是比较喜欢可视化的东西。但是随着接触到linux系统，服务器上到操作越来越多，这些windows上到工具，虽然能给工具带来很大的方便，但是在linux上却显得爱莫能助了。

<!--more-->

&emsp;&emsp;所以，最近开始全面切换git使用方式，统一使用命令行进行操作。这里总结了一些常用命令和一些使用技巧，希望你看了这篇文章之后，git command将不再是问题，还有一些骚操作哦！！

```
克隆git代码本地：git clone -b <分支名> [git项目地址] [指定本地路径]
初始化：git init       这个命令会在本地新建一个git仓库
与远程仓库相关联：git remote add origin git@xxx.com:xxx/xxx.git    这个命令可以将本地新建的git仓库，与远程仓库关联起来
查看工作目录和暂存区的状态：git status （可选参数：-uno   只显示在git版本管理中被修改到文件）
将工作目录添加到暂存区：git add    其中path可以是一个.号，表示添加所以修改项；也可以是一个具体到文件名，表示只添加这个文件；还可以是一个匹配文件或者目录到匹配符。
提交修改：git commit -m '提交到信息说明'   注意在commit之前需要先执行git add
覆盖上一次到提交：git commit --amend  有时候我们已经存在未push的commit，然后又有新的修改就可以使用这个命令，它会保留上一次的commit id和commit信息，直接添加新的修改内容，好处就是可以少很多commit，commit记录会看起来干净些。
推送commit到远程分支：git push origin <远程分支名>    如果本地当前分支已经和远程分支对应上到话，直接执行git push也是可以的，关联方式看下一个命令
本地分支与远程某分支关联起来：git --set-upstream origin <远程分支名>    关联之后，就不必每次push都加上远程分支名了，直接git push就行
新建本地分支：git branch <新的分支名>   这里新建分支是基于你执行此命令所在都分支建立的；也可以在新建分支的时候直接切换到新分支：git checkout -b <新的分支名>
新建基于远程分支的本地分支：git branch <新分支名> origin/分支名  这是基于远程的某个分支新建的分支，你也可以让它的名字远程分支一样，就相当于是拉去了一个远程分支到本地，不过push的时候一样需要和远程分支做关联；同样可以在新建分支的时候直接切换到新分支：git checkout -b <新分支名> origin/分支名
删除本地分支：git branch -d <分支名>    如果要删除的分支上有未合并的代码，会提示你将-d换成-D,也就是强制删除
删除远程分支： git push origin --delete <远程分支名> 
查看所有分支：git branch  <-a> 这里有个可选参数-a，不带参数会显示所有本地分支；带上参数-a就会显示所有本地加上所有远程的分支
合并分支：git merge <本地分支名 | origin/分支名>  它是将你填写的分支合并到你执行命令时所在到分支
切换分支： git checkout <本地分支名> 
撤销某个未add过的文件的修改： git checkout [要撤销的文件名]
直接用远程分支覆盖本地分支：git reset origin/分支名 --hard   在服务器上的时候，可能服务器上面出现有修改的代码，但是我们不需要提交到git仓库中，这个时候就可以直接执行这个命令来覆盖服务器上的分支代码。
查看diff：git diff commit-id1 | branch1 | tag1 commit-id2 --stat | branch2 | tag2    查看两个commit或者两个分支或者两个tag之间的改动文件
查看某个文件的改动记录：git log -p file

```

&emsp;&emsp;好了，差不多常用的就是这些，以后有需要再不上。最后附上一张git大全图：

![](/images/31.jpg)
