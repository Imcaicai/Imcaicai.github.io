# Git基本用法


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

##### 1. 修改分支名

```shell
git branch -m oldBranchName newBranchName
```

##### 2. 取消与远程仓库的关联

```shell
git remote remove origin
```

## 踩坑记录

> Please make sure you have the correct access rights
> and the repository exists.

[https://blog.csdn.net/jingtingfengguo/article/details/51892864](https://blog.csdn.net/jingtingfengguo/article/details/51892864)

> error: failed to push some refs to 'github.com:Imcaicai/BookRoad.git'
> hint: Updates were rejected because the remote contains work that you do
> hint: not have locally. This is usually caused by another repository pushing
> hint: to the same ref. You may want to first integrate the remote changes
> hint: (e.g., 'git pull ...') before pushing again.
> hint: See the 'Note about fast-forwards' in 'git push --help' for details.

