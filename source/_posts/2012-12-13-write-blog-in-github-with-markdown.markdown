---
layout: post
title: "write blog in github with markdown"
date: 2012-12-13 14:29
comments: true
categories: Octopress Markdown
---

# 安装与设置


## 安装和设置Git


## 安装Ruby环境

## 安装Octopress

先通过Git从Github上克隆一份Octopress
    git clone git://github.com/imathis/octopress.git octopress
<!--more-->

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
```sh
    rake new_post["title"]
    vim source/_post/2012-02-16-title.markdown
    rake generate
    rake preview
    rake deploy
```


# 其它功能组件

## Disqus(评论)

到[Disqus][d1]上设置一下, 如果没有账号可以免费注册一个. 在[设置][d2]设置
    Website name        NuoErlz Blog
    Website URL         http://nuoerlz.github.com/

编辑 _config.yml , 添加 Disqus 账号信息
    disqus_short_name: nuoerlz
    disqus_show_comment_count: true

[d1]: http://disqus.com
[d2]: http://nuoerlz.disqus.com/admin/settings/?p=general


## 分类(category)列表

### 增加category_list插件

保存以下代码到plugins/category_list_tag.rb:
```ruby
    module Jekyll
      class CategoryListTag < Liquid::Tag
        def render(context)
          html = ""
          categories = context.registers[:site].categories.keys
          categories.sort.each do |category|
            posts_in_category = context.registers[:site].categories[category].size
            category_dir = context.registers[:site].config['category_dir']
            category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase)
            html << "<li class='category'><a href='/#{category_url}/'>#{category} (#{posts_in_category})</a></li>\n"
          end
          html
        end
      end
    end

    Liquid::Template.register_tag('category_list', Jekyll::CategoryListTag)
```

这个插件会向liquid注册一个名为category_list的tag, 该tag就是以li的形式将站点所
有的category组织起来. 如果要将category加入到侧边导航栏, 需要增加一个aside.

### 增加aside

复制以下代码到source/_includes/asides/category_list.html.
```html
    <section>
      <h1>Categories</h1>
      <ul id="categories">
        \{% category_list %\}
      </ul>
    </section>
```

### 修改 _config.yml

配置侧边栏需要修改_config.yml文件, 修改其default_asides项:
```yml
    default_asides: [asides/category_list.html,asides/recent_posts.html, asides/github.html, asides/twitter.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]
```


## 主题

Octopress 可以安装不同的主题, 且安装过程很简单, 很值得一试. 首先先看一下都有什
么主题, 在[这里][tt1]可以查看.

主题的安装方法是:

* 找到这个主题的 github 地址, 比如 git://github.com/tommy351/Octopress-Theme-Slash.git
* 然后 clone 到你本地博客根目录的 .themes 目录中:
	git clone git://github.com/tommy351/Octopress-Theme-Slash.git .themes/slash
* 安装
	rake install['slash']
* 预览
	rake preview
* 部署上传
	rake deploy

[tt1]: https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes


# Reference

[搭Blog 学Git][r1]  
[如何维护Github上博客][r2]  
[为octopress添加分类(category)列表][r3]  

[r1]: http://shanewfx.github.com/blog/2012/02/16/bulid-blog-by-octopress/
[r2]: http://shanewfx.github.com/blog/2012/02/16/clone-blog-from-github/
[r3]: http://codemacro.com/2012/07/18/add-category-list-to-octopress/
