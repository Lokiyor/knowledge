## 1.git ignore不起作用  清除缓存
> git rm -r --cached .

> git add .

> git commit -m 'update .gitignore'

## 2.修改错分支 还没有commit，需要提交到其他分支
- 把当前修改 本地存储
> git stash
- 查看本地仓库信息
> git stash list
- 切换分支 从仓库中获取存储
> git stash pop
