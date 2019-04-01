---
layout: post
#标题配置
title:  热乎乎的经验教大家怎么搭建自己的个人博客
#时间配置
date:   2019-04-01 17:46:00 +0800
#大类配置
categories: ""
#小类配置
tag: 教程
---

* content
{:toc}


---
---

## 前期准备
&nbsp;
&emsp;&emsp;前期准备主要有两点，1、创建你自己的github项目；2、在本机安装好你的git。
&nbsp;

---

### github项目的创建
&nbsp;
&emsp;&emsp;首先登陆到你的github，点击右上角的加号，点击new repository进行新仓库的创建；<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://itmanmzt.github.io/styles/images/create-your-blog/001.jpg){:align="center"}<br>
&emsp;&emsp;之后需要主要仓库的命名，命名规则为：你的github名(如:itmanmzt).github.io，description可以随意填写，范围权限我们一般选择public，这样子别人也可以看到我们的项目，填写完毕之后点击create repository就创建好了。<br>
![](https://itmanmzt.github.io/styles/images/create-your-blog/002.jpg){:align="center"}
&nbsp;

---

### git的下载
&nbsp;
&emsp;&emsp;git的下载和安装和普通的软件安装没有太大的区别，只需要选择和自己的操作系统匹配的版本即可，这里给大家下载的链接。<br>
[git下载链接](https://git-scm.com/download/){:target="_blank"}<br>
&nbsp;

---
---

## 使用git在本地创建一个本地仓库并连接到GitHub的仓库
&nbsp;
+ 方式一：图形化界面直接创建一个文件夹作为本地仓库，右键点击Git Bash Here，进入命令行窗口，输入git init进行初始化，这个时候你回到文件夹设置为显示隐藏文件，你会发现有一个.git的隐藏文件，这时候说明初始化成功了。
&nbsp;
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/003.jpg){:align="center"}<br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/004.jpg){:align="center"}<br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/005.jpg){:align="center"}<br>
![step4](https://itmanmzt.github.io/styles/images/create-your-blog/006.jpg){:align="center"}<br>
&nbsp;
+ 方式二：命令行界面可以通过命令：mkdir /路径/文件夹名的方式创建文件夹,然后通过cd /路径/文件夹名进入到这个文件夹中，然后执行git init执行初始化。
&nbsp;
```linux
mkdir /g:/my repository
cd /g:/my repository
git init
```
&nbsp;
&emsp;&emsp;然后就是和GitHub建立连接。
&nbsp;
+ 第一步：获取SSH Key,在本地仓库点击右键点击Git GUI Here,点击Help,点击show SSH Key，然后复制该字符串。
&nbsp;
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/007.jpg){:align="center"}<br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/008.jpg){:align="center"}<br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/009.jpg){:align="center"}<br>
&nbsp;
+ 第二步：点击你GitHub的全局settings，点击SSH and GPG keys，点击New SSH key,粘贴你刚才复制的字符串，然后点击添加即可。
&nbsp;
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/010.jpg){:align="center"}<br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/011.jpg){:align="center"}<br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/012.jpg){:align="center"}<br>
![step4](https://itmanmzt.github.io/styles/images/create-your-blog/013.jpg){:align="center"}<br>
![step5](https://itmanmzt.github.io/styles/images/create-your-blog/014.jpg){:align="center"}<br>
&nbsp;
+ 第三步：在本地仓库创建一个README.md文件，作为一个项目说明文件，里面可以用文字介绍你的项目，然后通过命令行执行下面命令，SSH地址可以在GitHub对应的项目中找到。
&nbsp;
```linux
git add README.md //将要提交的文件添加到暂存区
git commit -m "提交说明" //将文件从暂存器提交到仓库，-m后面的内容是备注，可以区分不同提交的内容
git remote add origin git@github.com:itmanmzt/test.git //通过SSH地址和GitHub建立连接
git push -u origin master //将本地仓库提交到远程
```
&nbsp;
![](https://itmanmzt.github.io/styles/images/create-your-blog/015.jpg){:align="center"}<br>
&nbsp;
+ 到了这里我们就完成了仓库的创建和连接，nice!
&nbsp;