## 目录

- [配置信息](#配置信息)
  - [设置账号](#设置账号)
  - [检查已有的配置信息](#检查已有的配置信息)
- [分支管理](#分支管理)
  - [创建并切换分支](#创建并切换分支)
  - [把本地创建的分支推到远程](#把本地创建的分支推到远程)
  - [合并分支](#合并分支)
  - [查看本地和远程分支](#查看本地和远程分支)
  - [删除本地分支](#删除本地分支)
  - [删除远程分支](#删除远程分支)
- [其它](#其它)
  - [查看状态](#查看状态)
  - [add并commit](#add并commit)
  - [从远端拉取代码并合并到本地](#从远端拉取代码并合并到本地)
  - [如果冲突解决冲突后](#如果冲突解决冲突后)
  - [推送代码到远端](#推送代码到远端)



### 配置信息

#### 设置账号

```sh
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

#### 检查已有的配置信息

```sh
git config --list
```

### 分支管理

#### 创建并切换分支

```sh
git checkout -b branch-name
```

#### 合并分支

- 把 dev 分支合并到当前分支 

```sh
git merge --no-ff -m "merge dev" dev
```

#### 把本地创建的分支推到远程

```sh
git push origin branch-name
```

#### 查看本地和远程分支

```sh
git branch -a
```

#### 删除本地分支

```sh
git branch -D branch-name
```

#### 删除远程分支

```sh
git push origin --delete branch-name
```

### 其它

#### 查看状态

```sh
git status
```

#### add并commit

```sh
git commit -am "备注信息"
```

#### 从远端拉取代码并合并到本地

```sh
git pull origin branch-name --rebase
```

#### 如果冲突解决冲突后

```sh
git rebase --continue
```

#### 推送代码到远端

```sh
git push origin branch-name
```

