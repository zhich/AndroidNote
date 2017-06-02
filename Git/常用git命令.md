## 目录

- [配置信息](#配置信息)
  - [设置账号](#设置账号)
  - [检查已有的配置信息](#检查已有的配置信息)
- [分支管理](#分支管理)
  - [查看远程分支](#查看远程分支)
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

#### 查看远程分支

```sh
git branch -a
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

