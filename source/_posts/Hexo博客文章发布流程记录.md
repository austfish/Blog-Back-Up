title: Hexo博客文章发布流程记录
date: 2019-09-19 11:52:04
tags:
	- 工具
	- 记录
---

Hexo博客发布文章步骤：
1.新建文章
2.编辑内容
3.发布

<!--more-->

## 新建 hello 文件

    hexo new 'hello'

## 打开 hello.md 文件编辑

```
---
layout: post
title: 标题
date: 时间
tags:
	- 标签1
	- 标签2
---
摘要区域
<!--more-->

正文
```

## 发布博客

    $ hexo d -g

或者使用脚本

    ./deploy

```
#!/bin/bash
DIR=`dirname $0`

# Generate blog
hexo clean
hexo generate
sleep 5

# Deploy
hexo deploy
sleep 5

# Push hexo code
git add .
current_date=`date "+%Y-%m-%d %H:%M:%S"`
git commit -m "Blog updated: $current_date"

sleep 2
git push origin hexo

echo "=====>Finish!<====="
```

