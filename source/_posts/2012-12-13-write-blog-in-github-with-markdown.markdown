---
layout: post
title: "write blog in github with markdown"
date: 2012-12-13 14:29
comments: true
categories: Markdown
---

# 安装与设置
*****

1. 安装和设置Git


## 安装Ruby环境


## 安装Octopress


先通过Git从Github上克隆一份Octopress
    git clone git://github.com/imathis/octopress.git octopress

然后安装一些依赖的工具
    cd octopress
    ruby --version // Should report Ruby 1.9.x
    gem install bundler
    bundle install

安装 Octopress 默认的 Theme
    rake install


## 配置Octopress

通过修改[_config.yml][o1]来配置博客. 主要部分设置:
    url: http://nuoerlz.github.com
    title: NuoErlz Blog
    subtitle: It's me!
    author: nuoerlz
    simple_search: http://google.com/search

    # date format
    date_format: "ordinal"

    # Disqus Comments
    disqus_short_name:
    disqus_show_comment_count: false

[o1]: https://github.com/nuoerlz/nuoerlz.github.com/blob/source/_config.yml


# 写博与发博步骤
*****

## 开始写博文

通过`rake new_post["title"]`来在 source/_post 下创建一个新的Post, 文件名如下面
的格式:2012-02-16-title.markdown.
然后可以用 VIM 打开该文件, 并在其中输入/编辑文章内容.
    rake new_post["title"]
    vim source/_post/2012-02-16-title.markdown
...

写文章时, 可以使用 Markdown 和 Octopress Plugins 来对内容进行格式排版.


## 预览效果

在修改设置或者写完文章后, 想看看具体效果, 可以通过如下命令来完成:
    rake generate
    rake preview

然后在浏览器中打开`http://localhost:4000/`来看一看效果.

**如果对文章进行过修改, 也可以`rake generate`来更新.**


## 将博客部署到Github上

在预览的效果符合自己的预期后, 就可以通过如下命令将内容部署到Github上了.

第一次部署前需要先要在Github上创建一个 username.github.com 的[repository][g1],
然后通过`rake setup_github_pages`将自己的Blog与上述的repository关联起来. 在其
过程中根据提示输入 username.github.com .
    rake setup_github_pages
    > git@github.com:nuoerlz/nuoerlz.github.com

然后就可以通过下面的命令来部署自己的博客内容至Github了:
    rake deploy
    git status
    git add .
    git commit -a -m 'comment'
    git push origin source

[g1]: https://github.com/new


## 开始浏览自己的博客

在浏览器的地址栏中输入 username.github.com , 打开的网站就是自己的博客了.


## 发博主要步骤
    rake new_post["title"]
    vim source/_post/2012-02-16-title.markdown
    rake generate
    rake preview
    rake deploy


# 其它功能组件
*****

## Disqus

到[Disqus][d1]上设置一下, 如果没有账号可以免费注册一个. 在[设置][d2]设置
    Website name        NuoErlz Blog
    Website URL         http://nuoerlz.github.com/

编辑 _config.yml , 添加 Disqus 账号信息
    disqus_short_name: nuoerlz
    disqus_show_comment_count: true



[d1]: http://disqus.com
[d2]: http://nuoerlz.disqus.com/admin/settings/?p=general



# Reference
*****

[搭Blog 学Git][r1]

[如何维护Github上博客][r2]

[r1]: http://shanewfx.github.com/blog/2012/02/16/bulid-blog-by-octopress/
[r2]: http://shanewfx.github.com/blog/2012/02/16/clone-blog-from-github/
