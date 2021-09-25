---
title: '个人博客搭建与运维笔记'
date: '2020-01-07'
categories:
  - Hugo框架
tags:
  - Hugo框架
---


以前一般在CSDN上写博客，但现在CSDN广告越来越泛滥，无法忍受了。稍微研究了下目前可以写博客的平台 CSDN、简书、知乎甚至 微信里写文章，在看文章时都会受各种广告推荐的影响，记得有次在微信全神看推荐算法类的文章，突然中间给我插了条相亲的广告，当时非常恼火。

后来想了想各种博客平台为了赢利终将会采用各种广告的手段，所以还是自己搭建博客吧，而且对于一个做技术的人来说也确实有必要搭一个个人博客。下面就对我个人所面临的 框架选择、基础搭建过程、主题选择、部署和日常维护 做一些记录。



## 博客框架的选择

对于一个非专业Web开发的人来说，自己实施搭建成本太高了。所以一定要借用现有的开源框架来搭建，网上查了下主流框架主要有Jekyll、Hexo和Hugo，也看了很多人的blog，印象特深的是 [谢益辉](https://yihui.org/cn/about/) ，他也讲述了他用Jekyll的一些坑，然后转向Hugo框架，我觉得他的个人博客非常的nice，所以也顺着他的路用Hugo来搭建了。至于Hexo和Hugo的比较，目前来看基于Hexo还是比基于Hugo搭建的人要多，教程也是Hexo居多。但是Hugo目前也已经渐渐形成生态，基于Hugo搭建的主题也非常丰富 <sup>[6]</sup> ，Hugo也是以速度性能见长，所以当时也就基于Hugo来搭建个人博客了。

Hugo是由Go语言实现的静态网站生成器，还是比较简单容易上手的。官方提供的quick start <sup>[2]</sup> 也是可以在短时间内快速搭建一个个人网站的。

对于这篇blog而言，主要是Hugo框架学习和操作实践、主题选择方面的一些注意点和部署方面的实践。关于Hugo的使用Google下，也有很多的教程，我比较推荐官网的 quick start <sup>[2]</sup> 、 两个Youtube视频 <sup>[8] [9]</sup> 和 参考文献中的教程 <sup>[4] [5] [7] </sup> 。



## 基础搭建过程

这里主要介绍在应用主题前，一些基础环境的搭建。与官网提供的 quick start 教程 <sup>[2]</sup> 一致，这里对生成的目录做了稍微详细的讲解。

### 安装Hugo环境

```
$ brew install hugo
$ hugo version
```

以上通过 ```hugo version``` 来验证hugo环境安装是否成功。这里是基于macOS环境安装，其它操作系统参考 [官网安装](https://gohugo.io/getting-started/installing/) 。

### 生成个人网站

```
$ hugo new site blog_test
```

以上命令行会创建一个新的文件夹blog_test，该文件夹下包含Hugo最基本目录结构，其中blog_test目录下文件如下：

```
.
├ archetypes/
├ config.toml
├ content/
├ data/
├ layouts/
├ static/
└ themes/
```

生成文件包含6个文件夹 和 1个全局配置文件，下面依次对这7个文件做相应的说明。

**1. archetypes/ 目录**

新建文章时默认模板，当使用 ```hugo new``` 新建文章时会生成默认模板格式的文件 <sup>[3]</sup>，该文件夹配置的优先级高于文件夹 theme/ 下相应主题的 /archetypes 设置。一般情况下，/archetypes 下仅包含default.md文件，内容为：

```
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
---
```

当使用 ```hugo new``` 新建文章时，文章头部默认会有以上这段内容。其中包含标题（根据文件名推断而来）、日期及draft属性，一般我们会把draft属性去掉（**~~draft: true~~**），draft是草稿的意思，一般有这个属性的md文件不会生成显示，若不删除，则在生成网页时需通过 ```hugo -D``` 强制显示 <sup>[5]</sup> 。或者我们还可以直接使用主题里帮我们设置好了的 /archetypes 配置。

**2. config.toml 文件**

该文件为站点全局配置文件。该文件**非常重要**，直接决定网站最终的显示元素和布局 <sup>[7]</sup> 。配置参数也极其多，我自己只配置了非常重要的几个参数（会在下面配置主题中给出我的配置）。

**3. content/ 目录**

存放个人blog内容的目录，即我们所有写的文章都应存放在content目录下，也是Hugo默认的内容目录，类似于hexo的source <sup>[4]</sup> 。我们应该**重点组织好content目录内的内容**，后期在切换网页框架时（因为Web的开源框架也在日益升级迭代），也只需迁移content目录里的内容即可。

Hugo会将content/ 目录下的结构反映到生成的静态网站中 <sup>[4]</sup> ，这里以我自己网站举例如下：

```
.
└── content
    └── about.md                     // https://calxu.github.io/about/
    └── links.md                     // https://calxu.github.io/links/
    └── note
        ├── 20191101_shadowsocks.md  // https://calxu.github.io/note/20191101_shadowsocks/
        └── 20190810_git.md          // https://calxu.github.io/note/20190810_git/
```

**4. data/ 目录**

存储Hugo框架在生成网页时使用到的配置文件 <sup>[3]</sup> ，类似于hexo的source/_data <sup>[4]</sup> ，该文件夹默认为空。如果和我一样是一个Hugo入门新手，最好不要自己手动设置这个目录的配置，保持默认为空或者使用主题里设置好了的配置。

**5. layouts/ 目录**

存储 .html 网页内容的生成模版 <sup>[3]</sup>，该文件夹的优先级高于文件夹 theme/ 下相应主题下的 /layouts <sup>[4]</sup> ，该文件夹默认为空。一般不从事Web前端的开发，对于新手来说自己组织有点困难，通常保持该文件夹为空或者使用主题里设置好了的配置。

**6. static/ 目录**

存储 images、CSS和JS等静态文件 <sup>[3]</sup> ，当使用Hugo创建生成网页时，该目录下的文件会直接copy到 /public 目录下 <sup>[3]</sup> 。该文件夹的优先级高于文件夹 theme/ 下相应主题下的 /static 文件夹，该文件夹默认为空。一般不从事Web前端的开发，对于新手来写CSS和JS脚本会有点困难，我这里是直接应用主题里自带的 /static 。

**7. themes/ 目录**

存放网站主题的目录，themes目录下的每个子目录代表一个主题，可以在config.toml中通过参数theme指定主题（主题即theme目录下的子目录的名字）。



## 配置主题和生成博客

选择一个合适的主题作为自己的个人网站**非常重要**，官方网站提供了 [官方主题](https://themes.gohugo.io/) 有很多，有很多demo看似不错但代码方面的说明很少，改动起来需要深究代码细节，维护修改成本很高，很不适合新手快速搭建。这里提供一个 [Hugo主题使用排行榜](https://hugo-theme-ranking.oika.me/) 供大家参考下，一般排名Top的，发布者对代码方面的组织和说明会比较好，参考的博客样例也会更多，维护修改会相对会好很多。

我选择的主题是XMin，排名大概前30，也比较符合我的简约风格，功能完全够用并且代码比较精简，也无太多花哨臃肿的功能，并且发布者 [谢益辉](https://yihui.org/cn/about/) 在博客上组织也非常nice。所以当时综合可维护性和功能的考虑，选择了XMin主题，下面我主要介绍下我基于 [XMin主题](https://github.com/yihui/hugo-xmin) 的配置。

其中我主要关注3个文件，即 config.toml、content/ 和 themes/ 。其余文件夹（archetypes/、 data/ 、layouts/ 和 static/ ）在默认生成的情况下都为空文件夹，这些影响布局的属性主要依赖主题文件夹（即 themes/ ）对应主题的相应的配置值，下面主要介绍配置主题和生成博客的过程。

**1. 下载主题**

```
$ cd blog_test
$ git init           # 初始化仓库
$ git submodule add https://github.com/yihui/hugo-xmin.git themes/hugo-xmin   # 下载xmin主题
```

**2. config.toml 文件配置**

该文件用于Hugo Web网页的基本配置，配置文件细节因使用的主题不同而不同；我个人基于XMin的blog配置内容如下：

```
# 网站的根URL，需修改成自己的
baseURL = "https://calxu.github.io"
# 网站默认的语言，我一般是默认值
languageCode = "en-us"
# 首页的标题，需修改成自己的
title = "calxu"

# 我这里是使用的主题是hugo-xmin，网页的布局将使用对应主题的archetypes、layouts和static配置
theme = "hugo-xmin"

# 板块化分，weight对应在页面上显示的次序
[[menu.main]]
    name = "Home"
    url = "/"
    weight = 1
[[menu.main]]
    name = "Categories"
    url = "/categories/"
    weight = 2
[[menu.main]]
    name = "About"
    url = "/about/"
    weight = 3
[[menu.main]]
    name = "Links"
    url = "/links/"
    weight = 4
```

**3. 添加内容**

首先我这里删除默认的 archetypes/default.md 文件，因为 archetypes/ 比 theme/hugo-xmin/archetypes 里的优先级高（会覆盖主题里的设置），删除则会默认使用主题里的配置。

然后 ```hugo new note/20190107_ss.md``` 生成新的文件，再编辑加入新的内容，如下：

```
$ cat content/note/20190107_ss.md
---
title:  '利用开源ss工具快速搭建翻墙VPN'
date: '2019-11-01'
categories:
  - VPN
tags:
  - VPN
---

搭建ss的方法，网上有很多，请自行Google。
```

**4. 启动Hugo本地服务进行测试**

通过 ```hugo server -D``` 启动后台服务，通过 http://localhost:1313/ 可在本地进行查看网站；



## 部署与日常维护

当以上测试通过时，即可通过 ```hugo -D``` 生成相应的静态网页（静态网页默认存储在./public/目录下） <sup>[2]</sup> 。现在就涉及到将 ./public/ 目录下的静态网页部署的问题，有很多人将自己的个人网站部署在github上（通常以 github.io后缀的都是部署在github上的，比如 [样例1](https://jiyiren.github.io/) ，[样例2](https://ming1016.github.io/)），部署在github上实施成本较低，无需考虑后台服务和稳定性等问题，因为这些github都帮我们解决了。下面就部署在github上做一下简单的介绍：

**1. 创建GitHub Pages后台站点**

与创建普通仓库一样，不过通常将仓库命名为 username.github.io （我自己的博客是 calxu.github.io） ，并在仓库的 Setting 中设置 GitHub Pages 中的 Source 来源分支（我这里设置的是master branch）。这样后台设置就已经完成了，这里没有截图的说明，可以参考下 <sup>[10]</sup> ，[10] 虽是基于之前流行的框架Hexo进行搭建的，但是配置 GitHub Pages 部分是相通的。

**2. 部署**

通过 ```hugo -D``` 生成静态网页（即 ./public/ 目录的内容），将 ./public 里的内容单独 push 到对应的仓库分支（即我这里的 master branch ）就可以完成静态网页的部署，这里不会Git的同学还是自己学习下吧。

**3. 日常维护**

部署在github上总体来说是非常稳定的，基本不需要维护成本，但我习惯过一段时间整理下自己的 content 目录。后期主要是聚焦于博客的内容，通过```hugo new note/blog_content.md``` 生成新的blog，然后再去写内容，在写blog时，我个人比较习惯用 [Typora](https://www.typora.io/) Markdown编辑器，然后将写好的内容粘贴至 note/blog_content.md 中，后面再进行 push 至github端即可立即生效。



## 总结

这篇笔记主要记录了博客框架的选择、搭建过程和github部署作了简要的说明。搭建过程细化了官网提供的quick-start <sup>[2]</sup> 的过程，有2小点略有不同：(1) 对Hugo生成的目录结构作了一些操作上的说明；(2)这里在我自己选择的XMin主题上进行了定制化配置，对于其它主题操作基本是相通的。Hugo的功能非常的强大，我这里只是介绍了最基本的一些操作，一些特殊高阶的用法到后期再继续学习。

我正式工作时间也一年半了，越来越意识到写博客的重要性（由其对于一个程序员来说），简单来说有2小点理由：(1)写博客是对自己所做项目或一个技术理解的复盘总结；(2)当下很多时候不同工种的程序员互相不了解对方的技术栈，但写博客会让想了解你的人更加清楚你的技术栈。

通过这个笔记总结基本把我个人博客形式固化，后期就是主要聚焦在写文章，记录自己项目中的一些总结与技术心得，给自己立一个小的flag，争取1~2周写一篇技术的文章。



## 参考文献

1. [hugo官方主题](https://themes.gohugo.io/)
2. [hugo quick-start 官方文档](https://gohugo.io/getting-started/quick-start/)
3. [hugo官方目录结构](https://gohugo.io/getting-started/directory-structure/)
4. [Hugo从入门到会用](https://blog.olowolo.com/post/hugo-quick-start/)
5. [Hugo入门详细教程](https://youendless.com/post/hugo_base/)
6. [Hugo主题排行榜](https://hugo-theme-ranking.oika.me/)
7. [Hugo Universal主题使用指南](https://jimmysong.io/posts/hugo-universal-theme-guide/)
8. [Youtube Tutorial: Blog Hugo and Github Pages Part 1](https://www.youtube.com/watch?v=oUjk6wpJn7I)
9. [Youtube Tutorial: Blog Hugo and Github Pages Part 2](https://www.youtube.com/watch?v=4W9sVoVYMLo)
10. [GitHub+Hexo 搭建个人网站](https://segmentfault.com/a/1190000018879443)

