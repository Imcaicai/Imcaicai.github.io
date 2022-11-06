# Git基本用法&报错记录


## 基本流程

```shell
# 初始化仓库
git init
# 将本地库关联至远程仓库
git remote add origin git@github.com:....github.io.git
# 查看当前修改状态 
git status 
# 添加修改过得文件， . 表示所有，也可以指定文件 
git add . 
# ""里面的内容就是提交内容的说明信息 
git commit -m "first commit"

# 第一次提交方法1
git push -u -f origin main

#第一次提交方法2
git pull origin main --allow-unrelated-histories 
git push -u origin main

# 以后提交
git push
```

## 其他用法

#### 1 修改分支名

```shell
git branch -m oldBranchName newBranchName
```

#### 2 取消与远程仓库的关联

```shell
git remote remove origin
```

#### 3 实现本地库同时关联GitHub和Gitee

```shell
# 初始化仓库
git init
# 将本地库同时和GitHub、Gitee的远程仓库关联
git remote add github git@github.com:bertilchan/gitTest.git
git remote add gitee git@gitee.com:bertil/git-test.git
# 查看关联的远程库信息
git remote -v
# 添加修改，和原来一样
git add .
git commit -m "first commit"
# 分别提交
git push -u github main
git push -u gitee main
```



## 报错记录

### 报错1

> ssh: Could not resolve hostname github.com: Temporary failure in name resolution
> fatal: Could not read from remote repository.
>
> Please make sure you have the correct access rights
> and the repository exists.

[https://blog.csdn.net/jingtingfengguo/article/details/51892864](https://blog.csdn.net/jingtingfengguo/article/details/51892864)



### 报错2

> push后GitHub文件夹出现箭头且无法打开
>


