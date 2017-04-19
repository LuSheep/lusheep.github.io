---
layout:     post
title:      "我也有博客小站了"
subtitle:   "辛辛苦苦一两天，总算建好了"
date:       2017-04-19
author:     "Lusheep"
header-img: "img/post-bg-js-version.jpg"
tags:
    - 杂谈
---

> 开工没有回头箭，撸起袖子加油干！

#### 前言

Lusheep的Blog就这么开通了。

之前陆陆续续在CSDN写博客（我的<a href="http://blog.csdn.net/shakespeare001" target="_blank">CSDN主页</a>），在这个人人都可以轻松建站的时代，再不弄一套自己的博客小站就要跟不上时代节奏了~相比于在成熟的大型社区CSDN，自己弄一个小站人气肯定没有那么高，但是最大好处是可以自由定制啊，还可以使用Markdown写文章，看起来多酷！下面就来回顾一下我的建站历程：

#### 建站历程

我使用的是Github Pages + Jekyll 模式进行建站，将自己的博客托管在Github上，轻量、免费还易维护，支持Markdown书写，还有很多Jekyll模板供我们选择，多好。

```
请提前安装好Git工具
```

##### 第一步：在Github上创建repository

要开启Pages功能，我们需要按照这种格式`username.github.io`来命名我们的repository名称，其中username指的是我们GitHub上的用户名，当创建好这样的repository之后，Git会自动帮我们开通Github Pages功能（可以到setting中查看）。

在Github创建好repository之后，就可以clone到本地电脑，以后我们就先在本地通过markdown写好文章，然后提交到Github上即可。

##### 第二步：安装jekyll

上面我们只是创建好了仓库（repository），开通了Github Pages功能，要让Pages能够使我们创建的项目以网站的形式访问，需要jekyll的帮助。

> jekyll是一个静态网页的生成工具，或者说是一个文本转换引擎，它以特定的目录结构进行组织，而Github支持jekyll，就可以让我们以访问网站的形式访问我们的项目。

而jekyll是基于ruby编写，因此在使用jekyll之前我们需要先安装好ruby的环境。Windows下先去<a href="http://rubyinstaller.org/" target="_blank">rubyinstaller官网</a>下载，然后安装。

安装好ruby之后，还需要安装ruby的包管理工具<a href="https://rubygems.org/pages/download" target="_blank">RubyGems</a>，先去官网下载zip压缩包，解压之后运行其中的setup.rb文件即可。

准备工作完成之后，就可以开始安装jekyll了：

> 先进入命令窗口（cmd），通过命令：cd E:\blog\username.github.io进入到你clone下来的项目根目录，运行以下命令安装jekyll：gem install jekyll

是不是很简单？就是这么简单！至此，我们的准备工作已全部完成。

##### 第三步：选用满意的jekyll模板

jekyll是一个文本转换引擎，可以将我们用markdown、html等编写的文件通过相关规则转换成静态网页，因此它有特定的目录结构，如下：

```
|-- _config.yml  
|-- _includes  
|-- _layouts  
|   |-- default.html  
|   `-- post.html  
|-- _posts  
|   |-- 2016-08-08-first-blog.markdown
|   `-- 2017-04-19-test-test.markdown 
|-- _site  
`-- index.html  
```

其中：_config.yml是jekyll的配置文件，可以用来定义一些全局设置；includes文件夹里面放的是可重复使用的文件，如页面头部header.html、导航nav.html、底部footer.html等文件；layouts文件夹也是存放模板文件，在这些文件里面会通过{{content}}来加载其它数据到该模板文件中；posts文件夹，专门用来存放我们写的文章，命名必须要是`yyyy-MM-dd-filename.filetype`格式，filetype可以使markdown、md等形式。

因此，我们要到clone下来的项目根目录下创建这些文件（夹），是不是感觉有点麻烦？的确有点麻烦，如果我们有较高的审美能力、会前端、懂jekyll、想要自己从零捣鼓一个网站出来...那我们可以使用这种方式来建站。对于我们这种只想拥有现成的而且网站效果还不错的普通用户来说，我们可以去把别人的模板拿来为自己所用啊。有两种方式：

- 去jekyll模板网站选择自己满意的模板下载下来：http://jekyllthemes.org/
- 多看看别人的网站，然后到Github上clone下来

模板下好之后，将模板里面的文件拷贝到我们clone下来的项目根目录下，我们就在本地拥有了一个满意的博客小站了，在命令窗口下运行jekyll服务命令：`jekyll server`，然后在浏览器中输入http://127.0.0.1:4000，一回车，哈，我们的博客小站就出来了。看着是不是有点小激动呢~

不要激动太早，现在可是只有自己才能访问，还需要让别人能够访问才行啊，不过这一步简单，只需要通过Git工具将我们的项目push到Github上去就over了，这样，别人就可以通过username.github.io访问你的博客，而你只需要专注于写作即可。

##### 第四步：修改模板

我们下载的模板里面都是原有作者的内容，我们首先需要删除posts文件夹下的文件，因为这里面存放的原来作者的文章，可能还需要删除一些其他包含原有作者的信息。除了这些之外，我们需要根据自己的需求加入一些插件：

> 以我这个博客为例，我的模板是从Hux(黄玄)的<a href="https://github.com/Huxpro/huxpro.github.io" target="_blank">Github</a>上下载下来的（这里先感谢Hux提供的模板~）

- 修改评论插件

  原来的模板里面使用的是多说评论或disqus评论，但是多说评论即将在今年的6月停止服务了（不能变现的项目果然是比较难存活下去，可惜了...），而disqus是国外的服务，据说加载会较慢（没有实际测过，主要是一到添加应用的时候网页就会挂^~^）。上网查了一下，发现网易云跟帖插件还不错，因此我博客就用它了，添加也很简单，先去官网注册，然后获取代码添加到相应位置即可。

- 加入网站统计功能

  我使用的是百度统计，同样去官网注册，获取代码...

- 分享组件

  原模板中使用的多说分享组件，同样由于多说即将关闭服务的原因，这里使用的是百度社会化分享组件。至于如果添加就不用说了（去官网注册，获取代码...）。显示效果是这样的：

  ![分享组件](/img/baidu_share.jpg)

还有一小部分修改，主要看自己的需求了，里面的一些配置的作用多看几遍模板源码应该可以搞定。

#####  第五步：绑定域名

现在我们可以通过username.github.io来访问网站了，看着这个域名是不是有点不甘心...总想着能够用自己的个性域名去访问，这个问题其实也很好解决。我们要做的就是三件事：

- 去万网申请购买一个域名（也可以说其它平台，看自己需求）
- 在项目根目录下新建一个文本文件，里面添加一条A记录，其实就是把你申请的域名写在里面即可（如lusheep.me）。然后将这个文件命名为`CNAME`(不需要文件后缀名)。注意：这个文件很有可能在目标里面就已存在，你需要做的就是修改里面的内容即可。完成以后，记得push到Github上去。
- 到你购买域名的平台上，为这个域名添加一条解析记录，将这个域名的解析地址IP指向为：192.30.252.153或192.30.252.154

#### 后记

至此，一个博客小站就诞生了，后面我们可以阅读模板源码，尝试按照自己风格多多修改。但是，最重要的还是记住你搭建博客的初心：多写文章。

——Lusheep 于 2017.04.19