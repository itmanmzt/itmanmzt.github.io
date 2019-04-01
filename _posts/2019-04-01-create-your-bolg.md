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
<br>
&emsp;&emsp;前期准备主要有两点，1、创建你自己的github项目；2、在本机安装好你的git。
<br>

---

### github项目的创建
<br>
&emsp;&emsp;首先登陆到你的github，点击右上角的加号，点击new repository进行新仓库的创建；<br><br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://itmanmzt.github.io/styles/images/create-your-blog/001.jpg){:align="center"}<br><br>
&emsp;&emsp;之后需要主要仓库的命名，命名规则为：你的github名(如:itmanmzt).github.io，description可以随意填写，范围权限我们一般选择public，这样子别人也可以看到我们的项目，填写完毕之后点击create repository就创建好了。<br><br>
![](https://itmanmzt.github.io/styles/images/create-your-blog/002.jpg){:align="center"}
<br><br>

---

### git的下载
<br>
&emsp;&emsp;git的下载和安装和普通的软件安装没有太大的区别，只需要选择和自己的操作系统匹配的版本即可，这里给大家[git下载链接](https://git-scm.com/download/){:target="_blank"}<br>
<br>

---
---

## 使用git在本地创建一个本地仓库并连接到GitHub的仓库
<br>
+ 方式一：图形化界面直接创建一个文件夹作为本地仓库，右键点击Git Bash Here，进入命令行窗口，输入git init进行初始化，这个时候你回到文件夹设置为显示隐藏文件，你会发现有一个.git的隐藏文件，这时候说明初始化成功了。
<br><br>
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/003.jpg){:align="center"}<br>
<center>step1</center><br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/004.jpg){:align="center"}<br>
<center>step2</center><br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/005.jpg){:align="center"}<br>
<center>step3</center><br>
![step4](https://itmanmzt.github.io/styles/images/create-your-blog/006.jpg){:align="center"}<br>
<center>step4</center><br>
<br><br>
+ 方式二：命令行界面可以通过命令：mkdir /路径/文件夹名的方式创建文件夹,然后通过cd /路径/文件夹名进入到这个文件夹中，然后执行git init执行初始化。
<br><br>
```linux
mkdir /g:/my repository
cd /g:/my repository
git init
```
<br><br>
&emsp;&emsp;然后就是和GitHub建立连接。
<br><br>

+ 第一步：获取SSH Key,在本地仓库点击右键点击Git GUI Here,点击Help,点击show SSH Key，然后复制该字符串。
<br><br>
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/007.jpg){:align="center"}<br>
<center>step1</center><br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/008.jpg){:align="center"}<br>
<center>step2</center><br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/009.jpg){:align="center"}<br>
<center>step3</center><br>
<br><br>
+ 第二步：点击你GitHub的全局settings，点击SSH and GPG keys，点击New SSH key,粘贴你刚才复制的字符串，然后点击添加即可。
<br><br>
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/010.jpg){:align="center"}<br>
<center>step1</center><br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/011.jpg){:align="center"}<br>
<center>step2</center><br>
![step3](https://itmanmzt.github.io/styles/images/create-your-blog/012.jpg){:align="center"}<br>
<center>step3</center><br>
![step4](https://itmanmzt.github.io/styles/images/create-your-blog/013.jpg){:align="center"}<br>
<center>step4</center><br>
![step5](https://itmanmzt.github.io/styles/images/create-your-blog/014.jpg){:align="center"}<br>
<center>step5</center><br>
<br><br>
+ 第三步：通过命令行进行用户配置
<br><br>
```linux
git config –global user.name //name为你的Github用户名
git config –global user.email //email为你的Github邮箱地址
```
<br><br>
+ 第四步：在本地仓库创建一个README.md文件，作为一个项目说明文件，里面可以用文字介绍你的项目，然后通过命令行执行下面命令，SSH地址可以在GitHub对应的项目中找到。
<br><br>
```linux
git add README.md //将要提交的文件添加到暂存区
git commit -m "提交说明" //将文件从暂存器提交到仓库，-m后面的内容是备注，可以区分不同提交的内容
git remote add origin git@github.com:itmanmzt/test.git //通过SSH地址和GitHub建立连接
git push -u origin master //将本地仓库提交到远程
```
<br><br>
![](https://itmanmzt.github.io/styles/images/create-your-blog/015.jpg){:align="center"}<br>
<br><br>
&emsp;&emsp;到了这里我们就完成了仓库的创建和连接，nice!
<br><br>

---
---

## 使用jekyll来搭建博客
<br>
&emsp;&emsp;jekyll是一个简单的免费的Blog生成工具,将纯文本转化为静态网站和博客;由于咱们的GitHub Pages生成的是静态页面,每次更新博客都需要手动更改HTML,这就使得每次写博客都变得很麻烦,而用了这个工具以后,它会根据预先设置好的格式来生成博客内容,你就无需关心html代码,只需要把重心放在博客的写作上。
<br><br>
&emsp;&emsp;我们先进入jekyll的官网看看安装步骤，[jekyll中文官网](https://www.jekyll.com.cn/){:target="_blank"}&emsp;通过文档我们知道在安装jekyll之前需要先安装ruby和rubygem。<br><br>
![step1](https://itmanmzt.github.io/styles/images/create-your-blog/016.jpg){:align="center"}<br>

<center>step1</center><br>
![step2](https://itmanmzt.github.io/styles/images/create-your-blog/017.jpg){:align="center"}<br>
<center>step2</center><br>

---

### 排坑时刻

<br>
&emsp;&emsp;根据我安装jekyll的经验，我发现网上的很多教程都是错误的，安装完ruby和对应的devkit之后，虽然能检测到ruby和gem在本机上的安装版本，但在安装jekyll的时候却会因为gem的某些原因报错，报错内容如下：<br><br>

```
ERROR:  Error installing jekyll:
    ERROR: Failed to build gem native extension.

    current directory: /var/lib/gems/2.3.0/gems/ffi-1.9.18/ext/ffi_c
/usr/bin/ruby2.3 -r ./siteconf20170316-3892-175h62n.rb extconf.rb
mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h

extconf failed, exit code 1

Gem files will remain installed in /var/lib/gems/2.3.0/gems/ffi-1.9.18 for inspection.
Results logged to /var/lib/gems/2.3.0/extensions/x86_64-linux/2.3.0/ffi-1.9.18/gem_make.out
```
<br>
&emsp;&emsp;经过我在window和Linux系统上的各种尝试，虽然不清楚出现这个错误的原因是什么以及怎么解决这个错误问题，但我发现可以通过更换ruby的版本来解决这个问题，通过下载安装ruby2.3版本，[这是下载链接](http://www.ruby-lang.org/en/downloads/){:target="_blank"}，并且接下来具体的安装步骤在这里我就引用[正确的解决方法的文章](https://blog.csdn.net/Tang_Chuanlin/article/details/83542396){:target="_blank"}。
<br><br>

---

### 使用主题搭建个人博客

<br>

+ 第一步:去JekyllThemes下载一个自己喜欢的模板 。
+ 第二步:按照之前的步骤把下载好的模板上传到自己的Github仓库中 。
+ 第三步:在_posts文件夹中放入自己写好的博客,文件名必须是日期-标题名,例如:2016-10-25-我的第一篇博客 。
+ 第四步:上传博客到Github中即可访问自己的博客。

<br>

<br>

<br>

<center>有Marin的地方就有你的收获</center>

