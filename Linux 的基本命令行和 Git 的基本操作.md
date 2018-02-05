---
title: Linux 的基本命令行和 Git 的基本操作
date: 2017-11-27 16:28:16
tags: [Linux,Git]
---
# Linux 的基本命令行和 Git 的基本操作

## Linux 命令行
```
//进入目录
cd
//显示当前目录
pwd
//创建目录
mkdir 目录名
//创建目录
mkdir -p 目录路径
//查看路径
ls 路径
//查看路径
ls -a 路径
//查看路径
ls -l 路径
//查看路径 
ls -al 路径
//创建文件
echo '1' > 文件路径
//创建文件
echo '1' >! 文件路径
//创建文件
echo '1' >> 文件路径
//创建文件
touch 文件名
//改变文件更新时间
touch 文件名
//复制文件
cp 源路径 目标路径
//复制目录
cp -r 源路径 目标路径
//移动节点
mv 源路径 目标路径
//删除文件
rm 文件路径
//强制删除文件
rm -f 文件路径
//删除目录
rm -r 目录路径
//强制删除目录
rm -rf 目录路径
//查看目录结构
tree
//建立软链接
ln -s 真实文件 链接
```
## 怎么把项目上传到 GitHub
```
//把项目先克隆到本地
git clone git@github.com:FRANKIETANG/Remote-Mouse.git
//把文件夹里的东西全部清除，然后运行以下命令
git add *
git commit -m ‘del’
git push origin master
//把新项目放在文件夹，再运行以下命令
git add *
git commit -m ‘intinal’
git pull origin master
git push origin master
//这样，项目就可以在 GitHub 上看到啦哈哈哈
```
## 补充

[vim 的基本操作](https://coolshell.cn/articles/5426.html)

自制命令行

```
// ~/.bashrc

vi ~/.bashrc
alias xx = "要干的事情"
source ~/.bashrc
xx 

// ~/.zshrc

vi ~/.zshrc
alias xx = "要干的事情"
source ~/.zshrc
xx 
```

命令行小工具

- z: 方便实现快速目录跳转，[下载在此](https://github.com/rupa/z)
- fzf: 方便快速搜索文件或目录，[官网在此](https://github.com/junegunn/fzf#installation)

```
curl -L https://raw.githubusercontent.com/rupa/z/master/z.sh > z.sh
vi ~/.zshrc
source ~/z.sh
source ~/.zshrc
z // 看到你以前去过的所有目录
z Desk // 去桌面
```

```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

source ~/.zshrc
fzf
// 然后就可以在命令行搜索文件了
```

```
把 z 和 fzf 合在一起
vi ~/.zshrc

unalias z // 不要 z 命令
// 以下为合并命令，理解就好。把 z 换成 j 和 jj 。jj 是上一次搜索的记录
j() {
     if [[ -z "$*" ]]; then
         cd "$(_z -l 2>&1 | fzf +s | sed 's/^[0-9,.]* *//')"
     else
         _last_z_args="$@"
         _z "$@"
     fi
 }

 jj() {
     cd "$(_z -l 2>&1 | sed 's/^[0-9,.]* *//' | fzf -q $_last_z_args)"
 }
 
 source ~/.zshrc
```

```
// 安装 yarn
sudo apt-get update && sudo apt-get install yarn
```

```
// 怎么看精简文档
npm i -g tldr
// tldr = too long; didn't read
// 比如看 linux 下的 less 怎么使用
man less
// 太多了
tldr less
// 就会精简
tldr npm
```
```
// git 操作脱离民工三连的方法

git --version  // git 版本号


git config --global user.name xxx  // 全局设置 user.name
git config --global user.email yyy  // 全局设置 user.email
git config --global push.default simple
// 这个 simple 可以换成 matching，通常默认为是 simple 
// 意味着执行 git push 没有指定分支时，只有当前分支会被 push 到你使用 git pull 获取的代码。
// matching 参数是 Git 1.x 的默认行为，其意是如果你执行 git push 但没有指定分支，它将 push 所有你本地的分支到远程仓库中对应匹配的分支。
git config --global core.quotepath false // 防止文件名变成数字，因为有可能你的文件是中文上传的是后变成中文
git config --global push.editor "vim"  // 使用 vim 编辑提交信息
// 以上这些都是在编辑 ~/.gitconfig


mkdir git-demo
cd git-demo
git init  // 创建 .git 目录（本地仓库）
touch 1.txt  // 打开写点东西
git starus -sb  // ?? 1.txt  // 看文件处于什么状态
// 这个 -sb 不是傻逼的意思，s 是 summary，b 是 branch
git add .  // 将多行文字（注意是 行 文字）纳入 git 控制 
git starus -sb  // A 1.txt A 就是 ADD 的意思	M 就是改变的意思 
git commit -v  // 进入 vim 编gaibain辑，在第一行写字相当于 git commit -m "" 引号里的文字
git log  // 显示历史
git show  // 后面加 commit 的 ID 可以看到哪行文字改变
git remote add origin https://github.com/xxx/xxx.git  
// 与仓库建立链接，名字叫 origin，地址叫后面那个像网址一样的东西（错的）
git remote set-url origin git@github.com/xxx/xxx.git  // 更换地址
git push -u origin master  // -u 是本地的分支和远程的分支建立链接，master 是远程的分支
git clone  // 复制仓库并克隆，后面接仓库地址
git pull  // 更新本地仓库（.git）和本地文件
/*
1. git push 之前必须 git pull
2. git pull 之前必须 git commit
3. git commit 之前必须 git add
*/


// 以下命令 Google 点击第一个
git stash  // 不想提交进行了一半的工作
git branch  // 分支的新建
git checkout  // 切换分支
git merge  // 分支的合并
git reset  // 和 git checkout 差不多，细节 google
git reflog  // 用来数据恢复的
```
