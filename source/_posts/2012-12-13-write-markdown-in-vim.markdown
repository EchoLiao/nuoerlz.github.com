---
layout: post
title: "write markdown in vim"
date: 2012-12-13 18:36
comments: true
categories: Markdown VIM
---


# Browse & Edit & Navigation Markdown file in vim

## Syntax

[syntax vim script][s1]

add bellow line to ~/.vimrc
    au BufRead,BufNewFile *.{md,mdown,mkd,mkdn,markdown,mdwn} set filetype=markdown
<!--more-->

download and fix:
    cd /usr/local/share/vim/vimfiles/syntax/
    wget https://github.com/plasticboy/vim-markdown/blob/master/syntax/mkd.vim
    mv mkd.vim markdown.vim

open markdown.vim then edit it,
change the line:
    let b:current_syntax = "mkd"
to bellow line:
    let b:current_syntax = "markdown"

[s1]: https://github.com/plasticboy/vim-markdown/blob/master/syntax/mkd.vim


## Preview in browser

[preview][p1] : Plugin for previewing markup files(markdown,rdoc,textile,html)

install ruby 'bluecloth' gem
    // comment
    /usr/bin/gem install bluecloth

install vim plugin
    unzip vim-preview-0.9.zip -d /usr/local/share/vim/vimfiles/

add bellow line to .vimrc, so you can type <alt+I> to preview in browser:
    map <A-i> :Preview<cr>

[p1]: http://www.vim.org/scripts/script.php?script_id=3344


## Taglist Navigation for Markdown

[howto markdown in vim][t1]

add to .vimrc
    au BufRead,BufNewFile *.{md,mdown,mkd,mkdn,markdown,mdwn} set ft=markdown
    let tlist_markdown_settings = 'markdown;c:content;f:figures;t:tables;h:headlines'

add to .ctags
    --langdef=markdown
    --langmap=markdown:.md
    --regex-markdown=/^#[ \t]+(.*)/\1/h,heading1/
    --regex-markdown=/^##[ \t]+(.*)/.  \1/h,heading2/
    --regex-markdown=/^###[ \t]+(.*)/.    \1/h,heading3/
    --regex-markdown=/^####[ \t]+(.*)/.      \1/h,heading4/
    --regex-markdown=/^#####[ \t]+(.*)/.        \1/h,heading5/
    --regex-markdown=/^######[ \t]+(.*)/.          \1/h,heading6/
    --regex-markdown=/^#######[ \t]+(.*)/.            \1/h,heading7/

[t1]: https://github.com/hupili/evermd/tree/master/doc/howto-markdown-in-vim
