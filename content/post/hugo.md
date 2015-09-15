+++
date = "2015-09-14T19:54:07+02:00"
title = "Build blog with Hugo"
tags = [ "dev", "hugo", "web" ]
+++

This blog is created with *Hugo* - static website engine written in Go ([link](http://www.gohugo.io)).


### Requirements

* git
* Go 1.3+ ([install golang with gvm](/post/gvm))

### Installation

```bash
go get -v github.com/spf13/hugo
```

### Create new site

```bash
hugo new site /path/to/site && cd /path/to/site
```

### Create new post

```bash
hugo new post/first-post.md
```

### Install simple-a theme

```bash
git clone https://github.com/danielkraic/simple-a.git themes/simple-a
```

### Run hugo

```bash
hugo server --theme=simple-a --buildDrafts -w
```
