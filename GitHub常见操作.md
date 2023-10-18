
【知识点】GIT将本地的新分支同步到远程仓库中

切换到新分支，然后使用 `git push origin <新的分支名>` ，这样远程仓库里对应的分支就可以创建了。


【知识点】 GIT 将其他分支的内容合并到主分支上

切换到主分支
git checkout <目标分支>

git merge <源分支>

然后解决冲突；

【知识点】 git只克隆指定分支的东西

`git clone -b <分支名> <远程仓库地址>`

然后在该分支的基础上衍生新的分支： `git checkout -b <新分支>` 新建并切换到该分支


【知识点】 git重置操作

你想取消所以的修改，回到上一次提交： `git reset --hard`
不小心添加暂存区，想撤销： `git reset HEAD [file]` 不填写file则清空暂存区。


临时存储：

使用 `git stash` 命令
`git stash list` 显示所有的暂存结果
`git stash apply` 单纯恢复
`git stash drop` 单纯删除
`git stash pop` 恢复并删除stash的结果，相当于先 apply，然后 drop

# 恢复文件

修改了文件或者暂存了文件，使用 `git restore --staged` （如果文件暂存）和 `git restore` 来恢复到这次提交之前。 该指令在 git 2.23以下无效。

git checkout则是比较底层的一个指令。 

