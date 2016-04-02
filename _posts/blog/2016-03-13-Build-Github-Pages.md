---
layout:     post
title:      使用 Github 搭建自己的博客
category: blog
description: Github 作为开源项目的代码托管平台，同时也提供了 GitHub Pages.
---

## 写在前面
 
这篇文章主要介绍如何通过 GitHub 来搭建属于自己的 Blog ,在这次搭建 Blog 的过程中主要会用到以下几种技术：

* [Github Pages][]
* [Jekyll][]
* [Disqus][]

其中, [Github Pages][]是[Github](https://github.com)上用来搭建项目展示，做自己 Blog 的一个平台.[Jekyll][]是[Github][]上的一个开源项目，是一种博客的解析引擎，[Github][]使用[Jekyll][]来实现Github pages的解析.[Disqus][]是一个评论的插件，用来实现网页的评论功能.

## 动手搭建

### Github Pages

GitHub Pages分为两种，一种是个人的页面，这种是和自己的用户名相关的，比如`username.github.io`；另一种是项目的展示页，是和每个在Github上托管的项目相关的.

在自己Github的主页，建立一个使用自己用户名的，比如我的用户名是`moonsea`,那么我建立一个`moonsea.github.io`的项目
![Create-Reposity](/images/githubpages/16-04-02-1-create-resposity.JPG)
在项目的“设置”(Settings)里面，找到“Github Pages”，然后选择“Launch automatic page generator”,然后在里面随便选一个模板创建就行，接下来会把整个项目清空，然后使用Jekyll的模板.
![Automatic-Page-Generator](/images/githubpages/16-04-02-2-automatic-page-generator.JPG)
等待大概10分钟之后，访问`http://username.github.io`就可以了，这样一个最基本的Github Pages就创建好了.

* 在这里说明一下,很多Blog会介绍不使用`automatic page generator`,而是通过自己新建一个`index.html`来初始化项目，或者使用`gh-pages`分支来创建Jekyll.以上两种方法不建议使用，因为在我自己搭建的过程中，使用上述两种方法都没有成功，不是创建更新之后，页面并没有更新，或者是无法访问到`username.github.io`.相比上述两种方法，直接使用`automatic page generator`创建页面，不仅能够很快`username.github.io`访问，而且可以直接使用`master`分支来更更新项目，减少后续工作的麻烦.

### Jekyll模板系统

> **搭建本地的Jekyll环境**

下载[RubyInstaller][],根据自己的系统选择下载，我下载的是`Ruby 2.2.4 (x64)`,下载到本地之后进行安装，并加入系统的`PATH`.完成之后运行如下命令验证是否成功

    ruby -v

更新Ruby的Gem资源

    gem update --system

这时候系统可能会提示`Connection refused`之类的错误，这是因为天朝的墙太厚了，可以通过如下方法改成国内江浙沪包邮的源.如果没有提示错误，可以直接略过此步骤，直接跳到“安装Devkit”.

    gem sources --remove https://rubygems.org/
    gem sources -a https://ruby.taobao.org/

这个时候，如果没有错误，可以再次略过此步骤，进入“安装Devkit”，如果提示问题`server certificate B: certificate verify failed`,这是因为要想江浙沪包邮也是需要先提供证明的，那么可以通过如下方法让他相信你

* 下载[carcert.pem][],将此文件另存为`carcert.pem`,保存到Ruby安装目录下，当然也可以保存到其他目录下，只要你记住就好
* 在系统“环境变量”中添加`SSL_CERT_FILE`,路径选择刚才保存`carcert.pem`的路径
* 重新打开CMD窗口再次添加江浙沪包邮源，就可以成功了

    gem sources -a https://ruby.taobao.org/
    gem update --system

> **安装Devkit**

下载最新的[Devkit][],进入页面之后，选择和自己Ruby和系统对应版本的Devkit，比如我的是`Ruby-2.2.4`,系统是`64位`，那么我下载的是`DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe`
![devkit](/images/githubpages/16-04-02-3-devkit.JPG)
运行，选择解压路径。解压完成后，CMD进入Devkit相应解压路径，运行如下命令

    ruby dk.rb init
    ruby db.rk install

提示完成安装就可以了

> **Gem安装Jekyll**

在CMD窗口中运行如下命令

    gem install jekyll

验证是否安装成功

    jekyll --version

安装Rdiscount，用来解析Markdown

    gem install rdiscount

至此，Jekyll就安装完成了.

> **更新Github Pages**

使用git命令将项目源码同步到本地

    git clone https://github.com/moonsea/moonsea.github.io.git

进入项目文件夹，删除所有文件

然后将想要参考的项目源码同步下来，复制到自己项目的文件夹下

使用CMD窗口，进入自己的项目的文件夹，现在本地运行查看效果

    jekyll serve

根据自己的需要进行修改，修改之后提交到Github查看

    git add --all
    git commit -m "Init"
    git push

至此，一个Jekyll模板系统就已经搭建完成了.

## 致谢

本文主要参考了以下Blog

[1][[原]通过GitHub Pages建立个人站点（详细步骤）][1]  
[2][Ubuntu Install Rbenv & Ruby & Rails (Week I)][2]  
[3][使用Github Pages建独立博客][3]  
[4][server certificate B: certificate verify failed][4]


[Github Pages]: http://pages.github.com/ "Github Pages"
[Jekyll]:   https://github.com/mojombo/jekyll
[Disqus]: http://disqus.com "Disqus"
[RubyInstaller]: http://rubyinstaller.org/downloads/
[carcert.pem]: https://curl.haxx.se/ca/cacert.pem
[Devkit]: http://rubyinstaller.org/downloads
[1]: http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html
[2]: http://www.cnblogs.com/moonseazj/p/4821018.html
[3]: http://beiyuu.com/github-pages/
[4]:http://stackoverflow.com/questions/4528101/ssl-connect-returned-1-errno-0-state-sslv3-read-server-certificate-b-certificat