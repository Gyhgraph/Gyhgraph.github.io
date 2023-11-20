---
title: "使用GitHub Pages 和 Hugo 创建一个免费的静态博客"
date: "2022-01-19"
weight: 10 #pinned/ displayed top on the list by weight=<num>
# aliases: ["/first"]
tags: ["Hugo"]
categories: ["blog"]
#series: ["Themes Guide"]  #todo 添加名为‘系列’自定义分类
author: "Dm"
showToc: false
TocOpen: false
draft: false
description: "Posted on Jan 19th"
---

## 内容
- 概述
- 初始 GitHub 设置
- 安装雨果
- 创建一个基本站点
	- 初始化新的站点
	- 选择并安装网站的主题
	- 在本地测试新站点
- 创作网站
	- 配置
	- 原型
	- 写作
- 将内容发布到 GitHub Pages
	- 将开发储存库链接到 GitHub Pages 储存库
	- 发布网站
- The finished product

该博客为[[静态博客]],并使用[[Markdown]]语法文本编辑。

## 概述
> 涉及知识：
> - 桌面系统基本环境配置 ——Homebrew软件包管理程序、hugo *本文为UNIX or Linux*
> - hugo DOCS ——hugo架构、配置等相关文档
> - git 和 GitHub 的基本知识 ——创建与部署
> - HTML 和 CSS 的基本知识 ——对于优化博客主页等相关内容

本文是关于如何使用 Hogo 和 GitHub Pages 创建具备基本功能的博客的详细过程记录存档（本文主要参考[youngkin’s blog](https://youngkin.github.io/post/createafreeblogsite))。想要快速了解如何使用[Hogo](https://gohugo.io/getting-started/quick-start/),想要快速入门设置 [Hugo GitHub Pages](https://gohugo.io/hosting-on-github/) site。阅读本文后你基本可以自己完成构建自己的blog，也可以对构建 Hugo 静态博客有大致了解，需要一点好奇心与一点点的耐心😜。

GitHub Pages 的核心是 GitHub repo.存储库和已发布的网站的组合。这样做的一个结果是，对存储库主分支的任何提交都会立即发布。创建 GitHub Pages 网站的一种方法是创建关联的“开发”GitHub储存库，即本文所述。

Hugo 是一个开源的静态网站生成器。Hugo 是用于解释如何基于预定义模版创建网站的基础引擎。HTML 用于site布局。CSS 用于定义颜色、字体、字体大小和其他与布局无关的属性，这些属性定义了网站的外观。网站的页面定义本身，即网站的内容，是使用标准[[Markdown语法]]定义的。

Hugo 还附带了一个HTML 服务器，可以在开发过程中使用，以便对网页所做的任何更改提供及时反馈，前提是要避免使用不兼容或错误的设置 : ) 。Hugo 有两种使用方式：

1. 开发模式——在这种模式下，Hugo 使用它的 HTML 服务器来渲染页面，因为它们在开发过程中被修改。在开发过程中运行服务器可在对内容进行更改时提供即时反馈。
2. 构建模式——Hugo 还将基于主题的布局、CSS 和定义内容的降价文件生成一个完整的静态网站。此模式的输出就是将要发布的内容。（可见Hugo Command Line $ `hugo server`）

Hugo 主题以及网站的页面定义储存在我上面所说的“开发”存储库中。这是主要的创作空间。Hugo Web 服务器针对此存储库运行。通过 Git 子模块的逻辑，对开发储存库中主分支的最终提交更改立即在 GitHub Pages 储存库中[^可用]: 博客主页可能需要一点时间生成完成。

> 关于 Git 子模块的简要介绍：
> Git 子模块是一种将一个**托管**的 GitHub 存储库包含在另一个托管存储库中的方法。托管存储库的全部内容在**托管**存储中可用，就好像它只是托管存储库中的普通子目录一样。可以在托管存储库的上下文中对**托管**存储库进行更改。提交任何此类更改可立即在**托管**存储库中使用。

在子模块的上下文，GitHub Pages 储存库是托管存储库。它包含网站的已发布内容。相反，包含 Hugo 主题和内容开发版本的存储库是托管存储库。当网站内容准备好发布时，使用 Hugo 生成静态网站。Hugo 将内容生成到与托管的 GitHub Pages 存储库关联的子模块中。准备就绪后，更改会在托管存储库中提交，它们会立即可用，从而在 GitHub Pages 存储库中发布。

## 初始 GitHub 设置

需要创建两个存储库，GitHub Pages 存储库和托管开发存储库。GitHub Pages 存储库有 2个选项：

1. 个人或组织
2. 项目

有关差异的更多详细信息，请参阅[GitHub Pages 帮助文档](https://help.github.com/articles/user-organisation-and-project-pages/#user%E2%80%94organization-pages)。本文介绍如何创建个人GitHub 页面存储库。

创建 GitHub Pages 存储库相当简单，但它的名称有一个非常重要限制。存储库的名称必须采用`<github_username>.github.io`，GitHub Pages 存储库只是一个普通的GitHub存储库，但有一个具体限制的名称。*此repo为静态博客发布存储库，也即链接与托管存储库中的**托管**存储库*。参阅[GitHub Pages 快速入门指南](https://pages.github.com)。

第二步是创建将进行网站开发&创作的存储库。这也是一个普通的 GitHub 存储库。出于本文的目的，我们将使用’myblog.dev.repo’作为开发存储库的名称。参阅[GitHub 文档](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/)。

## 安装雨果

在 Terminal（终端） 安装 **Homebrew** [[Terminal\_in\_proxy]]
使用Command Line $ `brew install hugo`
*‌本文使用为UNIX or Linux如需Windows请使用浏览器搜索，win可以使用Chocolatey & $ `choco install hugo[-extended] -confirm`*

Docker 镜像也可使用。但涉及知识盲区，请参考[Hugo 文档](https://gohugo.io/getting-started/installing/)

## 创建一个基本站点 site

#### 初始化新站点

首先要做的是将开发&创作存储库克隆到本地：

`git clone https://github.com/<github_username>/myblog.dev.repo.git`

> 注意：
> git 使用GitHub HTTPS 通过 个人身份令牌PAT 来验证权限 [[GitHub\_DOCS]]
> 参阅[GitHub Docs](https://docs.github.com/en/)

在 GitHub 存储库设置和 Hugo 安装后，开始初始化site。Hugo 通过 `hugo` 命令行提供了这个功能：

1. ‘myblog.dev.repo’ 将作为存储库的根目录
2. run `hugo new site <myblog *新site的根目录* >`
----
即 $ hugo new site myblog
----
1. `hugo new site` 告诉 Hugo 创建一个新站点。而 ‘myblog’ 将作为新站点的根目录,并创建在存储库根目录中。

‘hugo new site <myblog>’ 创建以下目录结构
```
site_root
  |--layout
  |--content
  |--static
  |--data
  |--themes
  |--config.toml
  +--archetypes
```
这些文件及文件夹是：
- content - 这是正在开发的内容所在的位置，即站点页面的各种降价文件。
- static - 不会改变的内容，例如图像。
- themes - 网站主题所在的位置。
- config.toml - 站点的基本配置，例如站点名称. \*\* \\# .toml 为配置文件范式中的一种，还包括.yml等，它们具有不同的格式要求。请参阅Hugo文档\*\*
- archetypes-hugo new \<contentdir/contentfile.md\>命令用于创建新内容降价文件的模板

#### 选择并安装site的主题

下一步是选择要使用的主题。查看[Hugo 主题页面](https://themes.gohugo.io)并选择合适的主题。然后将它添加到开发/创作仓库中。

***此处省略，依据主题wiki***

#### 在本地测试新站点

简单配置,添加 content/post/testpage.md *依据实际文件夹结构*
接下来通过启动 Hugo Web 服务器并导航到开发站点来测试：

$ `hugo server`

在没有**ERROR**时，输出底部附近，会看到如下一行：

`Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)`
其中 `http://localhost:1313/`为导航地址，复制至浏览器来预览你的blog

## 创作网站

在发布实际site时，testpage.md 删除，不再需要。
#### 配置

如上，’config.toml’文件指定了 Hugo site 的基本配置。Hugo 的[入门文档](https://gohugo.io/getting-started/configuration/)详细介绍了配置。如下是基本但实用的配置。

```
baseURL= "https://<github_username>.github.io/"
title= "blog"
theme= ''
languageCode= ‘’
date= 2021-11-03T12:34:56+00:00
paginate= 5

[menu]
 [[menu.main]]
        identifier= 'archive'
        name= 'Archive'
        url= 'archive'
        weight= 10

[params]
title= "blog"
DateFormat= "Jan 2, 2006"
defaultTheme= 'auto'

[[params.label]]
text= "Home"
icon= '/apple-touch-icon.png'
iconHeight= 35

```

该配置的含义：

- baseurl 已发布网站的 URL。对于 GitHub Pages 来说，它的格式唯一。如`https://name.github.io`
- title 显示在网站每个页面左上角、标题区域的文本，它是一个按钮控件，带回主页，通常为你的名字。
- theme 网站使用的主题
- languageCode 指定网站主要语言的IETF语言标签。
- date 基本时间格式
- paginate 显示在主页的post条目数量

- menu 右上角菜单控件
- params 具体参数配置

具体配置介绍参见Hugo的[入门文档](https://gohugo.io/getting-started/configuration)

#### 原型 Archetypes

`hugo new <some-post-file>` 原型是 Hugo 在通过命令创建新网页时使用的模板 。
它在每一页的顶部都有一个“头条”部分，描述了一些基本特征，如页面标题、描述和日期。如果没有原型，则必须在每个新网页上手动键入或复制此“头等大事”。
虽然 Hugo 在 安装了默认原型’archetypes/default.md’，但可以创建自己的原型。文件位于’archetypes/post.md’ 这个名字 ’post.md’ 对雨果来说是 Hugo 在生成帖子时将使用的原型（如果存在）。还有其他对 Hugo 有意义的原型文件名和位置。可以在[Hugo Archetypes](https://gohugo.io/content-management/archetypes/)页面找到有关原型的更多详细信息。

示例如下：

```
---
title: "{{ replace .Name "-" " " | title }}"
#description: <descriptive text here>
date: {{ .Date }}
draft: true
toc: false
image: ""
tags: []
categories: []
---

# Descriptive text here...
<!--more-->
```


- `---`  这些行描绘了页面的“前事”部分
- `title: "{{ replace .Name "-" " " | title }}"` 指定将破折号 ('-') 替换为空格后的文件名生成页面标题。这是一种快速、方便的自动生成标题的方法。例如，’My-New-Post.md’在页面标题中变为“我的新帖子”。
- `description` 指定要在主页上的文章摘要中使用的文本。此文本不会显示在文章中。它与’# Descriptive text here...’ 文件中稍低的行有关。
- `date` 替换为帖子的创建日期。如果您想修改生成的日期，可以更改它，例如在更新页面之后。您可以对日期执行更复杂的操作，例如发布日期、上次修改日期、到期日期等。这些都有特殊含义。有关更多详细信息，请参阅[Hugo 文档](https://gohugo.io/getting-stated/configuration/#configure-dates)。
- `draft` 指定页面是处于草稿还是发布状态。查看在 Hugo Web 服务器上运行的站点时，可以看到处于草稿状态的页面，但在已发布的网站（例如 GitHub Pages）中将看不到它们。
- `toc` 如果`true`，这将在文章顶部创建一个目录。这是一个定制。
- `image` 指定要在页面顶部显示的图像。如果未指定，将使用在’config.toml‘中的’header\_image’。
- `tags`指定低级别主题分组的逗号分隔列表。例如，`tags: ["Colorado", "Utah", ...]`。
- ‘categories’ 指定一个以逗号分隔的高级主题分组列表。
- `# Descriptive text here...` & `<!--more-->` 这些行用作’description’“前事”部分中行的替代。如果所需的描述性文本数量超过了可以轻松放入’description’行中的数量，则可以使用此功能。`<!--more..>`用于指示此类描述性文本的结束。与`description`上面不同，此文本确实出现在文章中。该行和这些行的替代方法`description`是不指定任何一个。在这种情况下，文章的前 70 个单词将用于文章摘要。

#### 写作

所有创作内容使用Markdown语法编写，参阅[Hugo Markdown 指南](http://www.markdownguide.org/tools/hugo/)。使用 配置 Goldmark `unsafe`选项允许在内容中使用标准 HTML 标记。

## 将内容发布到 GitHub Pages

#### 将开发存储库链接到 GitHub Pages 存储库

该步骤只需执行一次。

- `clone` Pages 存储库到本地，并创建`mian`分支

```
cd GitHub #\ 一般情况下，开发repo与pages repo 在同一文件夹中
git clone https://github.com/..git #\ 复制`Code`链接
ls
cd <>.github.io #\
git checkout -b main
touch README.md
git status
git add .
git commit -m “adding readme”
git push origin #\ 初始化repo main分支
```

- 将创建的 Pages 存储库\`clone到开发存储库’public’中

```
cd ../
ls
cd <>.dev.repo/myblog #\ 导航到site文件夹
git submodule add -b main https://..git public

hugo -t <theme> #\ 用 Hugo 生成博客的静态文件 <theme>为你选定的主题名称
cd public
ls #\ 此时将可以看到全部静态文件
git remote -v #\ 判定 public中  ‘repo’ ‘git’ 逻辑
git status
git add .
git commit -m “init commit”
git push origin main #\ 推送博客完成，此步结束便可以刷新到你的blog主页
```

- 将创作存储库推送到 GitHub，后面新的’post‘ 可在该创作存储库‘content’中添加‘.md’文件完成并更新提交

```
cd <>.dev.repo
git status
git add .
git commit -m “init commit” #\ 此时会提示 ’added another git repository..‘ 提示为该存储库中存在另一个’git repo’选择提交主’.git’即可，即忽视附属’.git’
git push origin main
```

```
cd <>.dev.repo/myblog #\ 后续’post‘更新
hugo -D
git status
git add .
git commit -m “init commit”
git push origin main
```

## The finished product

这篇文章基本涵盖Hugo静态博客创建以及后期新post的提交流程。由于现在没有相关语言基础知识，暂时无法根据个人需要来mod或createnew blog主题，未来很长一段时间应该也不会，但已经满足目前对于博客的基本需要。
如果需要评论模块及更详细的配置可以参考Rich Youngkin关于此的[博客文章](https://youngkin.github.io/post/createafreeblogsite/)。
这篇博客是用来练习对Markdown的文章编辑。

## References 参考

- [Hugo Docs](https://gohugo.io/documentation/)
- [GitHub Docs](https://docs.github.com)
- [GitHub Blog](https://github.blog)
- [Rich Youngkin’blog](https://youngkin.github.io/post/createafreeblogsite/)
- [YouTube@Ryan Schachte](https://youtu.be/LIFvgrRxdt4)
- [Hugo Command Line](https://gohugo.io/commands/hugo/)
