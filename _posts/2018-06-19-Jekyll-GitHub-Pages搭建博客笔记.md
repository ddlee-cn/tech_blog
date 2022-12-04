---
title: Jekyll + GitHub Pages搭建博客笔记
layout: post
categories: [数字生活]
abbrlink: ed30ba89
date: 2018-06-19 23:52:40
excerpt: 有关Jekyll和GitHub Pages配合搭建独立博客的笔记。
---


### 了解Jekyll与GitHub Pages的工作原理

Jeklly是基于Ruby的静态网页生成器，可以用于从Markdown文件和Template生成静态网页。

GitHub Pages是GitHub对Repo的附加功能，可以渲染Markdown源文件和伺服生成的静态网页文件，用于部署和更新博客。

建站workflow：

1. 本地搭建Jeklly的运行环境
2. 选择Jeklly主题并进行个性化配置
3. 建立GitHub Repo并开启Pages功能
4. 建立单独的gh-pages分支并将修改后主题推送到远程

如果不需要个性主题，可直接在GitHub官方支持的主题中选择使用，也不需要搭建本地的Jeklly运行环境。

更新workflow：

1. 更新Markdown文件
2. 推送到远程



### 搭建Jekyll运行环境，安装主题（Linux）

#### Jekyll环境

参考：
- https://jekyllrb.com/docs/installation/
- https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/


Ruby依赖及环境变量配置：
```bash
sudo apt-get install ruby ruby-dev build-essential

echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME=$HOME/gems' >> ~/.bashrc
echo 'export PATH=$HOME/gems/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

（Optional）使用国内RubyGems和Bundle镜像（[TUNA](https://mirrors.tuna.tsinghua.edu.cn/help/rubygems/)）：

```bash
# rubygem
gem sources --add https://mirrors.tuna.tsinghua.edu.cn/rubygems/ --remove https://rubygems.org/
# bundle
bundle config mirror.https://rubygems.org https://mirrors.tuna.tsinghua.edu.cn/rubygems
```

安装Jekyll及Bundler
```
gem install jekyll bundler
```

#### 安装主题及其依赖

选择主题：http://jekyllthemes.org/

下载主题及安装主题所需依赖：
```bash
git clone theme-source-url path-of-theme-repo
cd path-of-theme-repo
bundle install
```

根据主题文档配置`_config.yml`后进行本地预览：
```
bundle exec jekyll serve
```
在浏览器打开http://localhost:4000 即可。


### 集成到GitHub Pages
参考：https://jekyllrb.com/docs/github-pages/

建立Repo存放配置过的主题文件，并将更新commit到新branch gh-pages上，即可去https://username.github.io/repo-name 预览效果。


### 小结

相比Hexo等，使用Jekyll配合GitHub Pages的优势是热更新，一次搭建后只需要更新Markdown文件，无需回到本地运行环境生成网页后推送。