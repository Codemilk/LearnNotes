# GitHub

## 目的

借用github托管代码.

## 基本概念

1. 仓库(Repository):就是你的项目，一个项目代表一个仓库，当你在github上面开元一个项目，那就必须新建一个`Repository`，如果你的开源项目多了，那你的仓库也就多了起来.
2. 收藏(Star):仓库主页`star`按钮，意思为收藏项目的人数，也方便你下次查看
3. 复制克隆(Fork):将一个项目克隆(`Fork`)的到自己的仓库，且是**独立的**仓库
4. 事务卡片(Issue): 发现代码BUG，但是目前没有成行的代码，需要讨论时候用

5. 发起请求(Pull Request):当你Fork来的项目，你觉得还不错，你可以通过`Pull Request` 来提交给项目原持有人审核，经过持有人同意，将可以将你的改进版，合并
6. 关注(Watch): 如果你watch某个项目，那以后这个项目每次更新你都会收到通知



## 仓库管理

**或者快捷键T/t来查找**

##  GitHub Issues

当你的代码出现bug，但是目前没有成型代码，需要讨论时用，或者开源项目出现问题时使用，别人可以在你`issue`下面创建issue，你可以收到通知，与建立`issuse`的人讨论，并关闭`issue`

## 安装git

## Git工作流程

## Git的初始化以及仓库的创建和(本地)操作

### 1. 基本信息设置

```Git
1.设置用户名
git config --global user.name 'username'
2.设置用户邮箱
git config --global user.email `username@qq.com`
3.查看配置：
git config --list
```



### 2.初始化一个新的Git仓库

```
1.创建文件夹(仓库)
mkdir 	`文件夹名字`
2.初始化仓库(执行成功会生成隐藏文件夹.git)
git init
```

### 3.向仓库添加文件

```
1.创建一个文件
touch  '文件名'
2.查看当前版本库状态
git status
3.将文件提交到缓存区
git add '文件名'
4.提交文件到仓库
git commit -m '文件描述'

```

### 5.删除仓库文件

```
1.删除本地文件
rm -rf '文件名'
2.从git(缓存区)中删除文件
git rm '文件名'
3.提交操作
git commit -m 	'描述'
```

## Git管理远程仓库

### Git克隆操作

#### 目的：

* 将远程仓库(github)对应的项目复制到本地

  

#### 命令:

#####  1.克隆

```
1.克隆
git clone 仓库地址
```



##### 2.向远程仓库提交

```
1.提交
git push
```

## Git本地与远程仓库同步操作

### 将本地仓库的远程分支更新成远程仓库的最新状态

```
git fetch
```

**注意：git fetch 不会做的事情**

他不会更新你的仓库的状态(`status`)和分支，也不会修改磁盘上的文，他只是将文件下载了下来.

### 合并远程分支：

```
git merge o/master
```

### git pull

```
git pull =git fech +git merge o/master
```

当你想要 push 你的新提交时，发现远程仓库在你上次拉取以后已经又有了改变，也就是说你的新 commit 是基于旧提交的修改，这种情况下 Git 是不允许你进行 push 操作的，你需要使自己的工作基于远程的提交，这个过程可以用以下命令：

* git fetch ;git merge o/master

* 其实就是git pull -r(r就是rebase)

  转载自：https://www.jianshu.com/p/b37ff443de15