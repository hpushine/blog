---
title: hexo迁移
date: 2018-03-21 20:14:45
description: 使用 Github+hexo 搭建一个博客要花不少时间的，如果更换电脑了怎么直接使用之前搭建博客的源文件，只要简单几步就可以在新的电脑上继续写文章，而不必在意环境搭建的繁琐了，以下提供两种实现方法！
tags:
---
## 操作步骤
### 一、安装必要软件
```
安装 Git 客户端
安装 node JS
```
### 二、在 github 官网添加新电脑产生的密钥

### 三、源文件拷贝
将原来电脑上个人博客目录下必要文件拷贝到新电脑上（比如D:/Blog目录下），注意无需拷贝全部，只拷如下几个文件：
```bash
_config.yml
package.json
scaffolds/
source/
themes/
```
### 四、安装 hexo
```
在 cmd 下输入指令安装 hexo：npm install hexo-cli -g

```
### 五、进入 D:/Blog 目录（拷贝到新电脑的目录），安装相关模块
```bash
npm install
npm install hexo-deployer-git --save  // 文章部署到 git 的模块
npm install hexo-generator-feed --save  // 建立 RSS 订阅（选择安装）
npm install hexo-generator-sitemap --save // 建立站点地图（选择安装）
```

### 六、部署发布文章
```
hexo clean   // 清除缓存 网页正常情况下可以忽略此条命令
hexo g       // 生成静态网页
hexo d       // 开始部署
```
## 第二种方法
具体的思路是：在生成的已经推到github上的hexo静态代码出建立一个分支，利用这个分支来管理自己hexo的源文件。如果能在刚刚配置hexo的时候就想好以后的迁移的问题就太好了，可以省掉很多麻烦。

具体的操作：
克隆gitHub上面生成的静态文件到本地
```
git clone https://github.com/yourname/hexo-test.github.io.git```
把克隆到本地的文件除了git的文件都删掉，找不到git的文件的话就到删了吧。不要用hexo init初始化。

将之前使用hexo写博客时候的整个目录（所有文件）搬过来。把该忽略的文件忽略了
```
touch .gitignore```
创建一个叫hexo的分支
```
git checkout -b hexo```
提交复制过来的文件到暂存区
```
git add --all```
提交
```
git commit -m "新建分支源文件"```
推送分支到github
```
git push --set-upstream origin hexo```
到这里基本上就搞定了，以后再推就可以直接git push了，hexo的操作跟以前一样。

今后无论什么时候想要在其他电脑上面用hexo写博客，就直接把创建的分支克隆下来，npm install安装依赖之后就可以用了。

克隆分支的操作
```
git clone -b hexo https://github.com/yourname/hexo-test.github.io.git```
因为上面创建的是一个名字叫hexo的分支，所以这里-b后面的是hexo，再把后面的gitHub的地址换成你自己的hexo博客的地址就可以了。

这样完了以后，每次写完博客发布之后不要忘了还要git push把源文件推到分支上。

原文链接：https://blog.csdn.net/lvonve/article/details/79587321
原文链接：https://www.jianshu.com/p/beb8d611340a