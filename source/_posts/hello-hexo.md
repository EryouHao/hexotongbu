---
title: Hexo搭建博客并实现多终端同步管理
date: 2016-06-25 18:29:26
tags: hexo git
categories: 于博客
---
【hello，hexo！】终于搞定了hexo博客多终端同步管理了，现整理出过程分享出来，同时自己也再顺一遍。如果有错误欢迎指正~。
<!-- more -->
### 软件与准备
1.node.js   [node.js中文网](http://nodejs.cn/)
2.git [git官网](https://git-scm.com/downloads) 如果下载速度过慢，请移步百度下载，只是版本差异
3.文本编辑器，个人用的sublime text（其实用记事本也可以）
4.注册github帐号
### 安装node与git
上面的准备工作都完成后就可以进行软件安装了。

 1. 安装node 。直接双击下载下来的额msi文件，一路下一步就ok了，安装完之后调出命令行或者node命令输入终端，输入`node -v`看到版本信息就表明安装成功了。
 2. 安装git 。直接双击下载下来的git安装程序，同node安装，安装完之后右键发现多了两个Git GUI Here 、Git Bash Here，点击后者，然后输入`git --version`出现版本信息就表明安装成功了。
 3. 选装cnpm。由于npm速度有时候令人堪忧，所以建议安装淘宝源的cnpm，在git bash中输入下面整段
``` bash
alias cnpm="npm --registry=https://registry.npm.taobao.org \
    --cache=$HOME/.npm/.cache/cnpm \
    --disturl=https://npm.taobao.org/dist \
    --userconfig=$HOME/.cnpmrc"
```
 安装完之后验证，输入：`cnpm info express`,若出现一大堆信息则表明成功了

### 安装hexo
 1. 全局安装。在git bash 下输入：`cnpm install -g hexo-cli`
 安装完之后，输入`hexo -v`，出现版本信息则表明安装成功
 2. 创建hexo项目。在g盘（你可以在任意盘，这里只是举个栗子^_^）新建一个文件夹hexo。进入到此文件夹右键点git bash，然后输入`hexo init`,走完命令行之后输入：`cnpm install`
 3. 安装完之后可以输入：`hexo server`启动本地服务器，在浏览器中输入：http://localhost:4000/ 来访问hexo博客。
 4. 到此，本地搭建已经成功。

### 更换主题
这里我就不细说更换主题的内容了。太长了，有空单聊。
### 创建github pages与配置ssh
 1. 创建博客仓库。登陆到github上新建一个仓库，名称为：你的用户名.github.io
 2. 本地生成ssh密钥。git bash下输入`ssh-keygen -t rsa -C "你的邮箱地址"`
 3. 上传公钥串。在你的电脑C：\ Users\你的计算机用户名\.ssh目录下打开刚刚生成的id_rsa.pub，复制里面的内容。然后在这里https://github.com/settings/keys 新建一个ssh key，title随便写，把公钥串粘贴到文本框，保存即可。
 
### 将本地博客同步到github上
 1. 安装必要插件。进入到本地的hexo目录，在git bash下分别输入`cnpm install hexo -server --save` 和`cnpm install hexo-deployer-git --save`
 2. 编辑博客配置文件：_config.yml,具体参考https://hexo.io/zh-cn/docs/configuration.html （最重要的是配置deploy）

``` bash
deploy:
  type: git
  repo: git@github.com:你的github用户名/你的github用户名.github.io.git
  branch: master
```
 3. 重新部署。在git bash中依次执行 `hexo clean`（清除旧的public生成文件）`hexo generate`（或者hexo g 生成静态文件）`hexo server`（本地预览）`hexo deploy`（部署到github上，第一次会有弹框提示，输入yes，点击ok）
 4. 提交成功后就可以访问了，比如我的是：http://eryouhao.github.io/
 至此，博客搭建结束！可以稍微休息下，庆祝一下了~.

### 多PC同步管理博客
休息结束，下面干货来了。很多人可能家里一台笔记本，公司一个台式机，想两个同时管理博客，同时达到备份的博客主题、文章、配置的目的。下面就介绍一下用github来备份博客并同步博客。
### A电脑备份博客内容到github
 1. 配置.gitignore文件。进入博客目录文件夹下，找到此文件，用sublime text 打开，在最后增加两行内容`/.deploy_git`和`/public`
 2. 初始化仓库。在博客根目录下，在git bash下依次执行`git init`和`git remote add origin <server>` <server>为远程仓库地址。
 3. 同步到远程仓库。gitbash下依次执行以下命令

``` bash
git add . #添加目录下所有文件
git commit -m "更新说明" #提交并添加更新说明
git push -u origin master #推送更新到远程仓库
```
### B电脑拉下远程仓库文件
在B电脑上同样先安装好node、git、ssh、hexo，然后建好hexo文件夹，安装好插件，（然后选做：将备份到远程仓库的文件及文件夹删除），然后执行以下命令：
``` bash
git init
git remote add origin <server>
git fetch --all
git reset --hard origin/master
```
### 发布博客后同步
在B电脑发布完博客之后，记得将博客备份同步到远程仓库
执行以下命令：
``` bash
git add .
#可以用git master 查看更改内容
git commit -m "更新信息"
git push -u origin master #以后每次提交可以直接git push
```
### 平时同步管理
每次想写博客时，先执行：`git pull`进行同步更新。发布完文章后同样按照上面的 发布博客后同步 同步到远程仓库。
### 平时常用命令整理
``` bash
git pull #同步更新
hexo new post "新建文章" #简写形式 hexo n "新建文章"
hexo clean #清除旧的public文件夹
hexo generate #生成静态文件 简写形式 hexo g
hexo deploy #发布到github上 简写形式 hexo d
git add . #添加更改文件到缓存区
git commit -m "更新说明" #提交到本地仓库
git push -u origin master #推送到远程仓库进行备份
```
### Ending
终于结束了。文章有点长，如果一气呵成的话，应该没有大问题。可能有一些地方粗略的提了一下，欢迎批评指正。

转载请注明出处：http://eryouhao.github.io/2016/06/25/hello-hexo/
作者博客：http://eryouhao.github.io 欢迎回访~。
