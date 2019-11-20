---
title: Hexo使用笔记
date: 2019年11月18日15:49:36
tags: 工具使用
---

## Preface
本文是一片自己使用过程笔记，程度有限，如果本文未能满足你的要求可移步文末链接，2个大神的教程。

## WHAT
**hexo不是博客系统，而是静态页面生成、上传的工具**

> hexo 可以理解为是基于node.js制作的一个博客工具，不是我们理解的一个开源的博客系统。它不需要部署到我们的服务器上，我们的服务器上保存的是hexo帮我们生成静态的html页面(hexo编译.md生成html)
**关键词：GITHUB PAGES -> 页面生成**，github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如,每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成**相关页面的生成**，然后再将有改动的页面提交到github。

官网： http://hexo.io
Github: https://github.com/hexojs/hexo
命令doc：https://hexo.io/zh-cn/docs/commands.html

---

## HOW
### INSTALL FOR MAC（鉴于篇幅原因，简单写，安装过程中的坑自行百度）
- step_1 > 
确保有一个git仓库。 repository创建规则，域名：githubUserName + ".github.io"，如：zj614android.github.io     //zj614android是我的github用户名。
电脑中需要已安装Git、Node.js(6.9以上，没有的百度装一下)
- step_2 > 安装hexo
npm install -g hexo 

- step_3 > cd /Desktop/Workspaces/hexo/  //随意新建一个Dir，用来放hexo文件

- step_4 > hexo init

- step_5 > hexo s //本地运行hexo服务器,跑起来表示安装ok
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
![](https://raw.githubusercontent.com/410799459pic/pics/master/img/20191118152526.jpg)

- step_6 > 目录说明，过一遍就行，忘记了回头来查
>
>|-- demo//项目跟目录名
|-- .gitignore//git时忽略的文件或目录
|-- package-lock.json
|-- package.json//应用程序的信息
|-- _config.yml//网站的配置信息
|-- scaffolds//模板文件夹，Hexo的模板是指在新建的markdown文件中默认填充的内容。
|   |-- draft.md
|   |-- page.md
|   |-- post.md//博文模板
|-- source//资源文件夹，存放用户资源
|   |-- _posts//博文目录
|       |-- hello-world.md//博文
|-- themes//主题文件夹，Hexo 会根据主题来生成静态页面
|-- landscape.//默认主题
...


- step_7 > 初始化配置，如果第一次预装是没有配置的，只显示一个地球的图片。具体配置些啥呢？最直接比如说主题，也就是基本的页面布局，再比如说你这个本地hexo和远程github的关联。这俩个是必修改的。配置文件-->  hexo根目录/_config.yml
> url：网站地址，必须修改，此处博文是托管在github上，故此使用http://你的网站名.github.io格式作为网站名字
language：语言，设置中文，根据需要修改，中文为zh-CN
注意：配置值与配置名需要隔一个空格，否则会编译报错
另外选择一个自己喜欢的主题也是必要的，这里就拿Next说，主要就2步，第一步先安装，Next主题的安装方式很简单，只需要在博客主目录下执行(git clone https://github.com/theme-next/hexo-theme-next themes/next
)；第二步就是在配置文件里进行一个theme的配置即可。


---
### Release  Blog
安装并跑起来之后，接下来就是发表博客了。
发表就是把本地文件A放到本地指定目录B下执行相应命令C，该文件就会推到我们之前安装并部署好的线上Github地址D了。

> ||||||||||||||||||||**查看目录**||||||||||||||||||||
cd blogs  //这个blogs是自己本地目录(2.1的step3)
ls
>
> _config.yml       
node_modules      
public            
themes
db.json           
package-lock.json
scaffolds         
yarn.lock
debug.log         
package.json      
source  //hexo子目录，文章在这个路径下，点进去
>
> ||||||||||||||||||||**cd进source目录**||||||||||||||||||||
> cd source/
> 
> _posts  //文章最终存储目录
about      
categories 
debug.log  
schedule   
tags
>
> ||||||||||||||||||||**发布文章到posts目录**||||||||||||||||||||
> 准备好一篇文章：本例中是《Hexo使用笔记.md》
> cd _posts/
localhost:_posts xx$ ls
Hexo基本操作.md          bst-tree_houjijiedian.md
bst-tree_delete.md       bst-tree_introduce.md
>
> 执行hexo g   //g即generate简写，生成静态文件
> 执行hexo d   //d即deploy，部署

---

主线流程就酱

## Q&A
### 发布文章时标题为Untitled
需要md文章头部条件如下语句：(注意冒号后面有空格，hexo内都是这样，注意一下就好)
```
title: Hexo使用笔记
date: 2019年11月18日15:49:36
tags: tool
```


### 换电脑之后如何维护管理博客
username.github.io这个项目并不包含我们博客的md源文件，它保存的是我们本地通过hexo生成的html文件，所以我们为了保险起见最好还是提交俩个repo。
既然要提交就需要注意ignore文件，目前建议的ignore配置文件：
```
/.deploy_git
/public
/_config.yml
```

更多请参考：
https://www.jianshu.com/p/a04d75ee9ab6 //关系
https://www.jianshu.com/p/7776b777f3a1 //ignore

### 更多请参考
hexo入门教程https://www.jianshu.com/p/f7a84b4c5bca
hexo使用教程：https://blinkfox.github.io/tags/Hexo/

