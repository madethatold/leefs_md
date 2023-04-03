[Git使用教程,最详细，最傻瓜，最浅显，真正手把手教 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/30044692)

## 基本配置

```shell
git config --global user.name "xxx"
git config --global user.email "xx@xx.com"
# 用户名和邮箱作为一个标识
cd D:/lifeng
# 进入某个目录
mkdir xx
# 创建一个目录，作版本库
pwd
# 显示当前的目录   
# /d/lifeng/leefs

git init
# 把这个目录变成git可以管理的仓库

```

## 基本操作

工作区：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。
版本库(Repository)：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

```shell
# 将文件添加至版本库中
git add test.md
# 将test.md添加到暂存区里面去，实际上就是把文件添加到暂存区。
git commit -m 'commit test'
# 把文件提交到仓库，实际上就是把暂存区的所有内容提交到当前分支上。

git remote add origin https://github.com/xxxxxxxxxx
git push -u origin master
# 由于远程库是空的，我们第一次推送master分支时，加上了 –u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
# 之后推送至github的操作为git push origin master


git branch dev
# 创建分支名为dev
git checkout dev
# 切换为dev分支
git checkout –b name
# 创建+切换分支
git branch
# 查看分支，会列出所有的分支，当前分支前面会添加一个星号

# 此时在dev分支编辑文件，并add，切换回master分支，内容并未被修改

git merge dev
# 在master分支上合并分支dev的内容（合并某分支到当前分支）
cat test.md
# 查看文档内容
git branch -d xx
# 删除某分支

```

## 解决分支间冲突

分支策略：首先master主分支应该是非常稳定的，也就是用来发布新版本，一般情况下不允许在上面干活，干活一般情况下在新建的dev分支上干活，干完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来。

```shell
git checkout -b dev1
# 创建并切换至分支dev1
git add xx.md
git commit -m 'dev1 edit'
# 修改并提交文档

git checkout master
# 切换回主分支
git add xx.md
git commit -m 'master edit'
# 修改并提交文档

git merge dev1
# 在master主分支合并分支dev1
# 此时会产生冲突,使用git status查看状态
# 修改后重新提交


```

## 删除一个文件

```shell
rm xx.md
git add xx.md
git commit -m 'delete xx.md'
git push origin master
```

