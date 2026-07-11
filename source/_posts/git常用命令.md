---
title: Git的常用命令
date: 2023-07-16
tags: [git]
---

git常用命令
1
2
3
4
5
6
7
> git remote add <name> url
git remote rm <name>
git remote -v    #查看远程信息
git branch (branchname)  #创建分支
git checkout (branchname)  切换分支
git branch -d (branchname) 删除分支命令
git remote rename old_name new_name  # 修改仓库名
git基本操作
git pull
git pull
命令用于从远程获取代码并合并本地的版本。
git pull
其实就是
git fetch
和
git merge FETCH_HEAD
的简写。
命令格式如下：
git pull <远程主机名> <远程分支名>:<本地分支名>
$ git pull
将远程主机 origin 的 master 分支拉取过来，与本地的 brantest 分支合并
git pull origin master:brantest
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
git pull origin master
git push
git push
命令用于从将本地的分支版本上传到远程并合并。
命令格式如下：
git push <远程主机名> <本地分支名>:<远程分支名>
如果本地分支名与远程分支名相同，则可以省略冒号：
git push <远程主机名> <本地分支名>
以下命令将本地的 master 分支推送到 origin 主机的 master 分支。
git push origin master
相等于：
git push origin master:master
如果本地版本与远程版本有差异，但又要强制推送可以使用 –force 参数：
git push –force origin master
删除主机的分支可以使用 –delete 参数，以下命令表示删除 origin 主机的 master 分支：
git push origin –delete master
git remote
git remote
命令用于用于管理 Git 仓库中的远程仓库。
git remote
命令提供了一些用于查看、添加、重命名和删除远程仓库的功能。
以下是 git remote 命令的常见用法：
git remote
：列出当前仓库中已配置的远程仓库。
git remote -v
：列出当前仓库中已配置的远程仓库，并显示它们的 URL。
git remote add <remote_name> <remote_url>
：添加一个新的远程仓库。指定一个远程仓库的名称和 URL，将其添加到当前仓库中。
git remote rename <old_name> <new_name>
：将已配置的远程仓库重命名。
git remote remove <remote_name>
：从当前仓库中删除指定的远程仓库。
git remote set-url <remote_name> <new_url>
：修改指定远程仓库的 URL。
git remote show <remote_name>
：显示指定远程仓库的详细信息，包括 URL 和跟踪分支。
以下列出了远程仓库、添加远程仓库、重命名远程仓库、删除远程仓库、修改远程仓库 URL 和查看远程仓库信息的用法：
1
2
3
4
5
6
7
git remote
git remote -v
git remote add origin https://github.com/user/repo.git
git remote rename origin new-origin
git remote remove new-origin
git remote set-url origin https://github.com/user/new-repo.git
git remote show origin
本章节内容我们将以 Github 作为远程仓库来操作
git remote -v #显示所有远程仓库
git remote -v
可以查看当前仓库中配置的远程仓库列表以及它们的 URL。
以下我们先载入远程仓库，然后查看信息：
1
2
3
4
5
6
7
$ **git clone** https:**//**github.com**/**tianqixin**/**runoob-git-test
$ **cd** runoob-git-test
$ **git remote** -v
origin https:**//**github.com**/**tianqixin**/**runoob-git-test **(**fetch**)**
origin https:**//**github.com**/**tianqixin**/**runoob-git-test **(**push**)**
**origin** 为远程地址的别名。
git remote show [remote]  #显示某个远程仓库的信息
例如：
1
2
3
4
5
6
7
$ git remote show https://github.com/tianqixin/runoob-git-test
* remote https://github.com/tianqixin/runoob-git-test
Fetch URL: https://github.com/tianqixin/runoob-git-test
Push  URL: https://github.com/tianqixin/runoob-git-test
HEAD branch: master
Local ref configured for 'git push':
master pushes to master (local out of date)
git remote add
#添加远程版本库
<remote_name>
：要添加的远程仓库的名称。通常，远程仓库的名称为
origin
，但你也可以自定义一个名称。
<remote_url>
：远程仓库的 URL。它可以是一个指向远程 Git 仓库的 HTTPS、SSH 或 Git 协议链接。
以下命令将向当前 Git 仓库添加一个名为 origin 的远程仓库，它的 URL 是
https://github.com/user/repo.git。
git remote add origin
https://github.com/user/repo.git
实例用法：
1
2
3
# 提交到 Github
$ git remote add origin git@github.com:tianqixin/runoob-git-test.git
$ git push -u origin master
添加远程仓库后，你就可以使用其他 Git 命令与远程仓库进行交互，例如推送本地代码到远程仓库、拉取远程仓库的代码等。
其他相关命令：
git remote rm name  # 删除远程仓库
git remote rename old_name new_name  # 修改仓库名
git reset
git reset 命令用于回退版本，可以指定退回某一次提交的版本。
git reset 命令语法格式如下：
git reset [–soft | –mixed | –hard] [HEAD]
–mixed
为默认，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。
git reset  [HEAD]
实例：
1
2
3
$ git reset HEAD^            # 回退所有内容到上一个版本
$ git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本
$ git  reset  052e           # 回退到指定版本
–soft
参数用于回退到某个版本：
git reset –soft HEAD
实例：
1
$ git reset --soft HEAD~3   # 回退上上上一个版本
–hard
参数撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交：
git reset –hard HEAD
实例：
1
2
3
$ git reset --hard HEAD~3  # 回退上上上一个版本
$ git reset –hard bae128  # 回退到某个版本回退点之前的所有信息。
$ git reset --hard origin/master    # 将本地的状态回退到和远程的一样
注意：
谨慎使用
–-hard
参数，它会删除回退点之前的所有信息。
HEAD 说明：
HEAD 表示当前版本
HEAD^ 上一个版本
HEAD^^ 上上一个版本
HEAD^^^ 上上上一个版本
以此类推…
可以使用 ～数字表示
HEAD~0 表示当前版本
HEAD~1 上一个版本
HEAD^2 上上一个版本
HEAD^3 上上上一个版本
以此类推…
git rm
git rm 命令用于删除文件。
如果只是简单地从工作目录中手工删除文件，运行
git status
时就会在
Changes not staged for commit
的提示。
git rm 删除文件有以下几种形式：
1、将文件从暂存区和工作区中删除：
git rm
以下实例从暂存区和工作区中删除 runoob.txt 文件：
git rm runoob.txt
如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项
-f
。
强行从暂存区和工作区中删除修改后的 runoob.txt 文件：
git rm -f runoob.txt
如果想把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用
–cached
选项即可：
1
git rm --cached <file>
以下实例从暂存区中删除 runoob.txt 文件：
git rm –cached runoob.txt
删除 hello.php 文件：
1
2
3
4
$ git rm hello.php
rm 'hello.php'
$ ls
README
文件从暂存区域移除，但工作区保留：
1
2
3
4
$ git rm --cached README
rm 'README'
$ ls
README
可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件：
git rm –r *
进入某个目录中，执行此语句，会删除该目录下的所有文件和子目录.
