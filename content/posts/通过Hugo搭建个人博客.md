---
title: "通过Hugo搭建个人博客"
date: 2021-12-11T23:30:33+08:00
draft: false
---

## 前言

近日通过 Hugo + GitHub Pages 搭建个人博客，踩了不少坑，在这里记录下来

## 一些问题

### 为什么搭建个人博客，而不是使用知乎、简书和博客园等平台？

- 个人博客更自由，不必担心被第三方删帖、监控
- 个人博客自定义性更强，样式更美观，你甚至可以自己开发一个主题

### 为什么使用 Hugo + GitHub Pages 搭建静态博客而非 Wordpress 动态博客？

- 静态博客无需服务器，费用低，不考虑域名费用的话，完全可以做到0成本
- 无需备案
- GitHub Page 的国内访问速度还不错，国外则快得多，如果采用服务器，不但费用高，且难以平衡国内外的访问速度
- 可以通过 markdown 语法写作，便于迁移备份，程序员的福音
- 可以用 Gitee 替换 GitHub

### 为什么使用 Hugo 而非 Hexo、Vuepress 等静态博客工具

- 目前Hugo的使用人数最多， GitHub 上 Hugo 的 star 数也最多，是静态博客的未来
- 相比 Hexo 等工具，部署和生成速度最快
- Hugo 支持实时预览
- 用近几年大火的 Go 语言开发，应该算是它的优点
- 写作和发布流程简单，易于掌握
- 拓展和主题生态强大
- [Hugo 和 Hexo 的区别](https://io-oi.me/tech/hugo-vs-hexo/)

## 博客搭建

### 1.安装 Hugo

对于不同的操作系统，安装过程可能会有所不同，以下给出的都是 macOS 下的安装过程。如果你使用的是 Linux 或 Windows，请自行查看 Hugo 官方文档或 Google 相关教程。此外，由于国内网络环境较差，请你务必具备魔法上网的能力

macOS：

你需要先安装 homebrew —— macOS 上的包管理器

然后在终端中输入如下命令

```shell
brew install hugo
```

其它操作系统的安装方法，详见[官方文档](https://gohugo.io/getting-started/installing)

安装完成后，可以通过`hugo version`命令检验

```shell
hugo version
hugo v0.89.4+extended darwin/amd64 BuildDate=unknown
```

### 2.创建个人博客文件

在你的个人电脑上，选择一个合适的路径，用于存放个人网站

例如，在我的用户主目录中，使用如下命令创建个人博客

```shell
cd ～ # 进入主目录
hugo new site blog # blog 可以被替换成任何你喜欢的名字
```

这样，在你选择的路径中会生成一个`blog`文件夹，里面存放着你的博客文件

文件夹内目录如下

```shell
cd blog # 进入 blog 文件夹
tree # 使用 tree 工具查看文件夹结构
.
├── archetypes # 存放 markdown 文章的模板
│   └── default.md # 这是 Hugo 默认的模板文件
├── config.toml # 个人博客的配置信息
├── content # 你用写的博客文章存放在这里
├── data # 存放 Hugo 数据
├── layouts # 存放自定义的view，可为空 
├── static # 存放图像、CNAME、css、js等资源，发布后该目录下所有资源将处于网页根目录
└── themes # 存放你安装的 Hugo 主题
```

### 3.安装主题

这里我选择[MemE](https://themes.gohugo.io/themes/hugo-theme-meme/)主题，一个优雅、简约、现代，令人惊艳的主题

主题文档见[此处](https://io-oi.me/tech/documentation-of-hugo-theme-meme/)

> 为了便于维护，你需要先安装 git

在终端中输入

```shell
cd blog # 进入blog文件夹
git init # git初始化
git branch -M main # 替换成 main 分支
git submodule add --depth 1 https://github.com/reuixiy/hugo-theme-meme.git themes/meme # 该命令在 themes 文件夹中安装主题
```

用 Git 的这种方式安装 MemE，有利于主题的更新，以及持续集成部署。

#### 更新提示

如何将 MemE 更新到最新版本？

```shell
git submodule update --rebase --remote
```

请在 GitHub 上关注（Watch）Hugo 和 MemE，如此你就能通过邮件及时获取到最新版本的通知。当然，你也可以通过查看 Releases 页面以获取相关的更新说明。此外，如果有任何问题，可以前往 Issues 页面提问

#### 替换 `conifg.toml`

可以将 blog 文件夹中的 `conifg.toml` 文件删除，然后把 `themes/meme/config-examples/zh-cn/`目录下的`conifg.toml` 文件复制到 blog 文件夹中

或者通过终端命令

```shell
rm config.toml && cp themes/meme/config-examples/zh-cn/config.toml config.toml 
```

### 4.新建一篇文章

由于 Hugo 并不会提供默认的示例文章，所以如果你想在安装和配置完后立即体验 MemE 的话，还需新建一篇文章和一个关于页面

```shell
hugo new posts/hello-world.md
hugo new about/_index.md
```

现在：

```shell
hugo server -D
```

不到 100 ms 后，在浏览器中打开 http://localhost:1313/，恭喜你！你已经成功在本地搭建好博客了🎉🎉🍻！享受 MemE 的简约和 Hugo 的飞速吧！

### 5.部署到GitHub Pages

ss

新建一篇文章，编辑内容。
本地预览网站呈现效果。
构建 Hugo 网站。
提交修改至 Git 本地库。
将修改推至远程库