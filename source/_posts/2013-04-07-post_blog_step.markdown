---
layout: post
title: "Post blog with octopress & github page"
date: 2013-04-07 17:24
comments: true
categories: Octopress blog
---


## Use rake*

	cd ..../nuoerlz.github.com
	rake new_post["title"]
	vim source/_post/2012-02-16-title.markdown
	rake generate
	rake preview
	rake deploy

<!--more-->

## Use gb_*

	// write
	gb_add_blog_to_lib.sh post_file.md

	// auto crontab
	gb_auto_push_github_blog.sh

	// list original file
	gb_list_blog_original_source_and_dir.sh
