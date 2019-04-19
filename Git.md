## Git配置

**配置文件级别以及存放**

- `/etc/gitconfig` 文件：系统中对所有用户都普遍适用的配置。若使用 `git config` 时用 `--system` 选项，读写的就是这个文件。
- `~/.gitconfig` 文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用 `--global` 选项，读写的就是这个文件。
- 当前项目的 Git 目录中的配置文件（也就是工作目录中的 `.git/config` 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 `.git/config` 里的配置会覆盖 `/etc/gitconfig` 中的同名变量。

**命令**

- git config --list：读取配置信息列表
- git config  user.name "yanlei"：写入用户名
- git config  user.name：读取用户名



## Git工作流程

![img](https://7n.w3cschool.cn/attachments/day_160929/201609291518243574.png)



## Git基本操作



### Git创建仓库

1. 本地创建一个仓库：进入某个dir，`git init`，然后把文件加入版本控制（add+commit）
2. 从现有仓库克隆：`git clone url new_proj_dir_name`



### Git工作区、暂存区和版本库的交互

![img](https://7n.w3cschool.cn/attachments/day_160929/201609291519223166.png)

**注意：**

1. 使用git reset HEAD file 命令取消暂存区的文件时，会讲该文件**重新放回工作区**。
2. git rm --cached 将文件从暂存区删除，并且从硬盘中删除并重建包含改动的该文件。
3. git rm -f 将文件从硬盘删除。



### Git查看修改状态

- `git status`：查看上一次commit之后的修改，包括添加到暂存区以及仍然在工作区的改动。输出信息中包含两条取消暂存区或者工作区的命令。
- `git status -s`：简洁化输出信息



### Git查看修改内容

git diff 来查看执行 git status 的结果的详细信息

- 尚未缓存的改动：git diff
- 查看已缓存的改动： git diff --cached
- 查看已缓存的与未缓存的所有改动：git diff HEAD
- 显示摘要而非整个 diff：git diff --stat



### Git分支管理

- git branch 列出所有本地分支
- git branch branchname 新建分支
- git branch -d branchanme 删除分支
- git checkout branchname 切换分支
- git checkout -b branchname 新建并切换分支
- git merge branchname 在当前分支上把branch name分支合并进来
- 如果有冲突并修改冲突后，git add 要告诉 Git 文件冲突已经解决，之后commit提交
- 合并冲突取消合并：git merge --abort



**合并 VS 变基**

变基的最终结果与合并很相似，都可以获得分支的更改。

差别在于 HEAD 所在的分支提交树已被重写，以致被合并分支成为了其提交历史的一部分。这使提交链更加线性，且更易阅读。

使用场合：

因为变基将重写提交历史，针对短期生命的本地分支使用变基，而对公开仓库的分支使用合并。



### Git查看提交历史

- git log 查看提交历史
- git log --oneline 提交历史的简洁版本
- git log --oneline -5 只显示5个记录
- git log --oneline --graph 查看历史中什么时候出现了分支、合并
- git log --reverse 逆向排序，默认顺序是最近的提交为第一个
- git log --author=Linus 只显示指定用户的提交记录
- 指定日期的选项：--since --before --until --after ，举例：git log --oneline --before={3.weeks.ago} --after={2010-04-18}
- --no-merges 选项以隐藏合并提交
- --all 选项确使我们看到所有分支。默认只显示当前分支。
- 格式化输出：git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short



### Git标签

- git tag -a tag_name -m "some_msg" 给当前提交创建标签
- git tag -a tag_name 85fc7e7 给历史提交创建标签
- git tag 查看所有标签
- git tag -d tag_name 删除标签
- git log --oneline --decorate 提交历史中显示标签
- git checkout tag_name 切换到tag对应的提交



### Git远程仓库

**Github配置验证信息**

1. 本地生成SSH key：$ ssh-keygen -t rsa -C "youremail@example.com"
2. 要求确认路径和输入密码，使用默认的一路回车就行
3. 复制 ～/.ssh/id_rsa.pub 中的key
4. 粘贴到Github账户配置-SSH
5. 验证是否成功：$ ssh -T git@github.com

**Github建立远端仓库**

**本地与远端仓库交互**

1. 添加远程库：git remote add origin git@github.com:xx/xx.git
2. 提交推送至远程库：git push -u origin master
3. 查看远程库：git remote，-v选项可以显示 alias 的url信息
4. 删除远程库：git remote rm origin
5. 获取远程库更新：git fetch origin + git merge origin/master 或者 git pull
6. 推送更新到远程库：git push [alias] [branch]



### Git撤销提交

**git revert**

- git revert HEAD --no-edit 撤销最近的提交，**错误提交和“撤销”提交在历史中都可见**
- git revert [hash] 撤销提交历史中的任何一个提交

**git reset**

- git reset [HEAD/tag_name/hash] 撤回到该提交，撤回的部分放在工作区，提交历史不存在
- git reset [HEAD/tag_name/hash] --hard 同上，但是撤回的部分丢弃

**修正并覆盖上次提交**

- git commit --amend -m "some msg"



### Git 常用命令速查

![img](https://7n.w3cschool.cn/attachments/image/20170206/1486348362884912.jpg)



## Git裸仓库

作用：相当于在本地建立了 线上git 服务器，用于共享。



## Git 命令设置别名

**~/.gitconfig**

```shell
[alias]
  co = checkout
  ci = commit
  st = status
  br = branch
  hist = log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
  type = cat-file -t
  dump = cat-file -p
```

**Shell .profile**

```shell
alias gs='git status '
alias ga='git add '
alias gb='git branch '
alias gc='git commit'
alias gd='git diff'
alias go='git checkout '
```



## Github 

- Git 代码仓库托管
- 基本的 Web 管理界面
- watch 关注动态
- 讨论组
- 文本渲染（Markdown 等）
- 在线文件编辑器
- 协作图谱（报表）
- 代码片段分享（Gist）

**同步 fork来的仓库**

- ```shell
  $ git remote add upstream https://some_url
  $ git fetch upstream
  $ git checkout master
  $ git rebase upstream/master
  $ git push -f origin master
  ```



## 参考链接

Git 完整命令手册地址：[http://git-scm.com/docs](https://git-scm.com/docs)
