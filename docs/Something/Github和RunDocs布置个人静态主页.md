# Github和RunDocs布置个人静态主页

## 1.默认有GitHub账号，已安装git

## 2.准备仓库

### 2.1 新建一个repository

### 2.2 新建文件夹\docs

###	2.3 新建分支`gh-pages`，存放网页相关文件

### 2.4 在repo的settings里选择GitHub pages，选定上述新建的文件夹和分支

### 2.5 需要有一个index.html

## 3.准备RunDocs

### 3.1 在\docs下新建四个文件

```
//Gemfile
source "https://gems.ruby-china.com"
gem "jekyll-rtd-theme"

gem "github-pages", group: :jekyll_plugins

//Makefile
DEBUG=JEKYLL_GITHUB_TOKEN=blank PAGES_API_URL=http://0.0.0.0

default:
	@gem install jekyll bundler && bundle install

update:
	@bundle update

clean:
	@bundle exec jekyll clean

build: clean
	@${DEBUG} bundle exec jekyll build --profile --config _config.yml,.debug.yml

server: clean
	@${DEBUG} bundle exec jekyll server --livereload --config _config.yml,.debug.yml
	
//_config.yml
title: Your project name
lang: en
description: a catchy description for your project

remote_theme: rundocs/jekyll-rtd-theme

readme_index:
  with_frontmatter: true

exclude:
  - Makefile
  - CNAME
  - Gemfile
  - Gemfile.lock
  
//.debug.yml
remote_theme: false

theme: jekyll-rtd-theme  
```

### 3.2 界面说明：\docs子文件夹对应左侧导航栏一级页面

### 3.3 子文件夹中的README.md文件是一级页面命名和排序的关键

​				首先在前三行中写明，代表这个一级页面排在左侧导航栏的第1个（第二个一级页面就改成2，以此类推）

```markdown
---
sort: 1
---

# 你的标题
```

### 3.4 每个二级子页面就是这个一级页面文件夹下的markdown文件

## 4.可能会遇到的一些问题

### 4.1 解决 ! [rejected] xxx -＞ xxx(fetch first)问题

​		执行完`git pull --rebase origin master` 后，再执行`git push -u origin master` 

​				出现这个错误的原因：github中的README.md文件不在本地代码目录中

### 4.2 第一次push会遇到需要账号密码的情况，需要在GitHub账户设置里申请token

## 5.关于git的一些指令

### 5.1 新建代码库

```
git init //在当前目录新建一个代码库
git init [project-name] //新建一个目录，将其初始化为git代码库
git clone [url] //下载项目
```

### 5.2 配置

```
//git配置文件为.gitconfig，它可以在用户主目录下进行全局配置，也可以在项目目录下进行单独的项目配置
git config --list //显示当前git配置
git config -e [–global] //编辑git配置文件
git config [–global] user.name “[name]” //设置提交代码时的用户名
git config [–global] user.email “[email address]” //设置提交代码时的用户邮箱
```

### 5.3 对文件的增删改操作

```
git status //查看状态
git diff //查看变更内容
git add [file1] [file2] … //添加指定文件到暂存区
git add [dir] //添加指定目录到暂存区，包括子目录
git add . //添加当前目录下所有文件到暂存区
git add -p //添加前会要求确认
git rm [file1] [file2] … //删除工作区指定文件，并将删除放入暂存区
git rm --cached [file] //停止追踪指定文件，但该文件会保留在工作区
git mv [file-original] [file-renamed] //修改文件名，并放入暂存区
```

### 5.4 代码提交

```
git commit -m [message] //代码从暂存区提交至仓库区
git commit [file1] [file2] … -m [message] //提交暂存区的指定文件到仓库区
git commit -a //直接将工作区中自上一次commit之后所作的更改到仓库区
git commit -v //提交时显示所有diff信息
git commit --amend -m [message] //使用一次新的commit，替代上一次commit，如果代码没有任何变化，则用来改写上一次commit的提交信息
```

### 5.5 分支

```
git branch // 显示所有本地分支
git branch -r //列出所有远程分支
git branch -a //列出所有本地分支和远程分支
git branch [branch-name] //新建一个分支，但依然停留在当前分支
git branch --track [branch] [remote-branch] // 新建一个分支，并与指定的远程分支建立追踪关系
git branch -d [branch-name] // 删除分支
git push origin --delete [branch-name] // 删除远程分支
git branch -a | grep “feature_201906.*” | xargs git branch -D //按关键字匹配，批量删除本地分支
git branch -a | grep -o “feature_201905.*” | xargs -I {} git push origin :{} //按关键字匹配，批量删除远程分支
git checkout -b [branch] // 新建一个分支，并切换到该分支
git checkout [branch-name] // 切换到指定分支，并更新工作区
git checkout - // 切换到上一个分支
git merge [branch] // 合并指定分支到当前分支
git rebase [branch] // 衍合指定分支到当前分支
git remote update origin --prune //获取最新的远程分支列表
```

### 5.6 远程操作

```
git fetch [remote] // 下载远程仓库所有更改
git pull [remote] [branch] // 拉取远程仓库的更改，并与本地分支合并
git remote -v :// 显示所有远程仓库
git remote show [remote] // 显示某个远程仓库的信息
git remote add [shortname] [branch] // 新建一个远程仓库，并命名
git push [remote] [branch] // 上传本地指定分支到远程仓库
git push [remote] --force // 强行推送当前分支到远程仓库，即使有冲突
git push --all // 推送所有分支到远程仓库
```

### 5.7 撤销

```
git checkout [file] // 恢复暂存区指定文件到工作区
git reset [file] // 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
git reset --hard // 重置暂存区与工作区，以上一次commit保持一致
git reset --hard HEAD // 撤销工作目录中所有未提交文件的修改内容
git reset [commit] // 重置当前分支的指针为指定的commit，同时重置暂存区，但工作区不变
git reset --hard [commit] // 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
git stash // 暂时将未提交的变化移除
git stash pop // 将暂时未提交的变化移入
find . -name “.git” | xargs rm -Rf //删除本地.git文件
```

### 5.8 查看信息

```
git log // 显示当前分支的版本历史
git log --stat :// 显示commit历史，以及每次commit发生更改的文件
git log -S [keyword] // 根据关键字，搜索提交历史
git log [tag] HEAD --pretty=format:%s // 显示某个commit之后所有的变动，每个commit占据一行
git shortlog -sn // 显示所有提交过的用户，按提交次数排序
git blame [file] // 显示指定文件是什么人在什么时间修改过
$ git log --author="user" --pretty=tformat: --numstat | gawk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "增加的行数:%s 删除的行数:%s 总行数: %s\n",add,subs,loc }' //统计某个用户提交的代码量
git log --author="user" --since=yyyy-mm-dd --until=yyyy-mm-dd --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' //统计某个用户某个时间段提交的代码量
```

