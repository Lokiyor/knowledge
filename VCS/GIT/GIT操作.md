# GIT的一些常用操作
## git关于tag的相关操作
### 查看tag
#### 查看全部tag
```
> git tag (--list)
```
#### 筛选过滤tag(-l命令)
```
> git tag -l "v3.3.*"
```

### 增加tag
#### 打标签
```
> git tag -a v1.0.0.0 -m “Release version 1.0.0.0″
```
参数说明:
- git tag: 是命令
- -a: 增加名为v1.0.0.0的标签
- -m: 后面跟着的是标签的注释

#### 推送tag到远程服务器
```
## 推送当个标签
> git push origin v1.0.0.0
## 推送本体所有的标签
> git push origin --tags
```

### 删除tag
#### 删除本地tag
```
> git tag -d v0.1.2 
```
#### 删除远程tag
```
> git push origin :refs/tags/v0.1.2
```

### 根据tag打分支
#### 切换tag
切换到具体的tag,这个时候不位于任何分支，处于游离状态，可以考虑基于这个tag创建一个分支。
```
> git checkout v1.0.0.0
```
#### 根据某个tag打出新分支
```
## 打出分支
> git branch hotfix-20200324 v1.0.0.0
## 切换分支
> git checkout hotfix-20200324
## 提交远程
git push origin hotfix-20200324
```


## 其他操作
### git ignore不起作用  清除缓存
```
> git rm -r --cached .
```
```
> git add .
```
```
> git commit -m 'update .gitignore'
```

### 修改错分支 还没有commit，需要提交到其他分支
- 把当前修改 本地存储
```
> git stash
```
- 查看本地仓库信息
```
> git stash list
```
- 切换分支 从仓库中获取存储
```
> git stash pop
```
