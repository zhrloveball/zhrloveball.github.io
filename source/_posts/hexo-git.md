---
title: 使用Github Pages和Hexo搭建博客(更新：换电脑迁移博客)
date: 2018-01-05 13:20:42
tags: 
  - hexo
  - github
categories:
  - 工具
---

搭建个人博客有点晚了， 但是Better later than never，下面开始开荒种田。

<!--more-->

### <font color = "#159957">20190717更新，备份网站源码至GitHub</font>

在github上新建一个分支，每次发布前将源代码提交到该分支，再发布网站，参考：[使用hexo，如果换了电脑怎么更新博客？ - CrazyMilk的回答 - 知乎](https://www.zhihu.com/question/21193762/answer/79109280)


### <font color = "#159957">20181223更新，Mac下报错 xcrun: error: invalid active developer path</font>

![](/images/article_pictures/hexo_error_1.jpg)

可能是MacOS升级后带来的问题：提交博客内容到Git时报错，在终端中执行 ``xcode-select --install`` 重新安装Xcode再重新提交。




### <font color = "#159957">20180723更新，换电脑后博客迁移</font>
根据第一次安装步骤在新电脑上安装Git(及秘钥),NodeJs,Hexo，将原来博客下的scaffolds(文章模板)、source(博客文章)、themes(主题)、_config.yml(博客配置文件)、package.json(安装包名称)拷到新电脑博客文件夹下。若执行``hexo clean && hexo g &&   hexo d``命令不能提交到GitHub上需手动push，执行如下命令:

```
cd myBlog/public
git init
git add .
git commit -m "move blog to Mac"
git remote add origin git@github.com:zhrloveball/zhrloveball.github.io.git
git -f push origin master
```

若push速度很慢，进行如下操作：
- 访问ipaddress网站 https://www.ipaddress.com/，或者 http://ip.tool.chinaz.com/
- 查询github.global.ssl.fastly.net和github.com的ip地址
- 通过sudo vim命令 修改/etc/hosts文件(Mac系统)，将github.com的ip映射添加到hosts中
- 更新DNS缓存 ``sudo dscacheutil -flushcache``，重新push

### 准备工作

#### 安装Git

* [下载地址](http://gitforwindows.org/) 
* 安装完毕后，打开Git Bash，输入命令，查看版本
```bash
$ git version
```

#### 安装Node.js

Hexo是基于NodeJs的静态博客,安装Hexo需先安装NodeJs
* NodeJs官网下载很慢，这里从 [Node.js中文网](http://nodejs.cn/download/)下载
* 在Custom Setup这一步选 Add to PATH 添加环境变量
* 查看版本:
```bash
$ node -v
```

#### 安装Hexo

默认安装很慢，可先切换npm源，加速安装过程

```bash
$ npm config set registry https://registry.npm.taobao.org
$ sudo npm install -g hexo
```

若警告This package is no longer maintained没有关系，耐心等待即可

#### 配置Hexo

创建一个文件夹如F:\myblog用来存放博客内容，右击文件夹Git Bash Here,初始化Hexo：

```bash
$ hexo init
```

初始化完毕后myblog文件夹下会多出配置文件等，可参考 [Hexo参考文档](https://hexo.io/zh-cn/docs/setup.html)

#### 配置Github

##### 创建仓库

  在Github中创建一个仓库，名称为 yourname.github.io 把 yourname 替换成你的Github名称

##### 配置账户

在Git Bash中配置你的Github账户信息

```bash
git config --global user.name "yourname"
git config --global user.email "youremail"
```

##### 通过SSH Key验证身份

###### 生成秘钥

在Git Bash中输入以下命令，生成秘钥

```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```

出现 "Enter file in which to save the key" ,直接回车选择默认位置
出现 "Enter passphrase(empty for no passphrase)" ,直接回车不设置密码

###### 查找秘钥

进入默认地址

```bash
$ cd ~/.ssh
$ cat id_rsa.pub
```

###### 在Github中添加SSH Key

在Github中依次 点击头像 - Settings - SSH and GPG keys - New SSH key，Title随便，将SSH Key(以ssh-rsa开头)拷贝进去 

###### 验证是否添加成功

在Git Bash中输入以下命令

```bash
$ ssh -T git@github.com
```

#### 部署

##### 修改配置文件

进入myblog文件夹，修改 _config.yml 中的 deploy 节点，将repo改为创建的仓库地址

```
deploy:
  type: git
  repo: https://github.com/YourgithubName/YourgithubName.github.io.git
  branch: master
```

【**注意**】 .yml文件对格式要求严格，type: 、repository: 、branch: 前面两个空格，冒号后面一个空格

##### 安装依赖包

输入命令 

```bash
npm install hexo-deployer-git --save
```

【**注意**】必须进入存放hexo的文件夹(我的是F:\myblog)路径下执行该命令
【**注意**】若出现错误 

```bash
npm ERR! code EPERM
npm ERR! errno -4048
npm ERR! syscall lstat
```

可以输入以下命令再重试 

```bash
npm cache verify
```

##### 部署

输入命令 
```bash
hexo clean && hexo g && hexo d
```
hexo g是hexo generate的缩写，详见[hexo命令](https://hexo.io/zh-cn/docs/commands.html) 

【**注意**】必须进入存放hexo的文件夹(我的是F:\myblog)路径下执行该命令
【**注意**】若出现错误 fatal: could not read Username for 'https://github.com': No error ，把 _config.yml 文件中  repo : https://github.com/yourname/yourname.github.io.git 这个地址改为 git@github.com:yourname/yourname.github.io.git 即可

##### 验证

在浏览器输入 yourname.github.io，查看你的博客

### 开始写博客

#### 新建文章

输入命令
```bash
hexo new "文章名"
```
source/_posts 路径下会生成相应的md文件，参考[Hexo基本操作:写作](https://hexo.io/zh-cn/docs/writing.html)

#### 本地预览

书写完毕后，可先在本地预览，输入

```bash
$ hexo clean && hexo g && hexo s
```
打开 http://localhost:4000/

#### 部署到Github

参考 6.3 和 6.4

### 修改Hexo主题

如果觉得Hexo默认主题不合适，可以换成其他主题，这里使用Next主题，使用步骤可以浏览 [Next官方文档](http://theme-next.iissnan.com/getting-started.html)

### 后记
搭建博客并没有想象中难，因为大神们已经把轮子造好了，后续还可以绑定个人域名等