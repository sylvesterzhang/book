---
layout: post
title: jekyll安装使用
tags: 其他
categories: Common
---

jekyll是一个博客工具，将markdown文件生成静态网页，具有较好的迁移性。

**安装依赖包**

Ruby

RubyGems

NodeJs

Python

安装完成后重启电脑

**配置gem镜像**

```
$ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
$ gem sources -l
```

**安装jeckyll-pagination**

```
$ gem install jekyll-pagination
```



**安装jekyll**

```
$ gem install jekyll
```

---

**写文章**

`1`在_posts中创建文件，格式为“year-month-day-title”，不可含中文

`2`文件内需要配置头信息才可被识别

```
layout: post
title: GIT使用经验汇总
tags: Linux
categories: Common
```

`3`使用git同步到服务器

```
$ git add .
$ git commit -m"add one article"
$ git push
```

`4`在git服务器上更新静态文件



