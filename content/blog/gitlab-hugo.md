+++
banner = "img/banners/banner-3.jpg"
categories = ["开始"]
date = "2017-08-03T13:39:46+02:00"
tags = ["技术"]
title = "在GITLAB 部署HUGO博客"

+++
## 安装知识

使用GitLab Pages部署hugo博客只需要把博客源代码Push到Repo，然后配置好GitLab Continuous Integration(GitLab CI)，就会自动渲染和部署hugo博客。

<!--more-->

GitLab Pages可以绑定域名，而且可以上传自定义SSL证书，本文使用的是CloudFlare的免费SSL证书。

在GitHub上搭建hugo博客只能本地渲染成HTML文件，然后hugo d把渲染后的文件push到repo。如果需要管理博客源文件就需要新建一个Repo，或者新建一个分支管理博客源代码。使用GitHub另外一个缺点就是GitHub Pages自定义域名就不能强制HTTPS。

## hugo初始化

在本地先初始化hugo目录，建议参考官网教程，本文就不详细介绍。

    hugo  new site blog
    hugo server

需要把主题中的 .git 删除。

## GitLab仓库

* 新建一个公开的仓库，命名为yourname.gitlab.io，其中yourname是你的GitLab用户名（下文同样）。同时在GitLab设置中添加你的SSH秘钥，然后就可以本地hugo目录和远程仓库相关联起来。

    cd existing_folder
    git init
    git remote add origin git@gitlab.com:yourname/yourname.gitlab.io.git

* 在hugo博客根目录中新建文件.gitlab-ci.yml。
* 添加.gitlab-ci.yml就可以把博客源代码 push 到 repo 中。

``` 
git add .
git commit -m"hugo init"
git push origin master 
```

在gitlab仓库 Pipelines 中查看到 hugo 渲染和部署的过程。

* 如果构建失败，你会收到项目构建失败的邮件，你可以在 Pipelines 中查看构建失败的原因，比如.gitlab-ci.yml中script缺失了必须的插件。构建成功后，可以访问yourname.gitlab.io查看搭建效果。

## 绑定域名

* 想要使用CloudFlare的HTTPS服务，就必须使用它的nameserver，也就是使用它的域名解析服务。

    irma.ns.cloudflare.com
    jay.ns.cloudflare.com
    添加一条A记录，指向52.167.214.135（**自行ping**），然后添加一条 CNAME 记录，把 www 子域名设置成 yourname.gitlab.io 。

* 生成密匙证书

当你的域名接入之后，点击 Crypto ，找到 Origin Certificates ，点击 Create Certificate 。

## CloudFlare设置

绑定完域名和设置完 HTTPS 之后，还需要设置规则，强制整个网站 HTTPS 化，以及使用301重定向把 no-www 重定向到 www 。
在CloudFlare中找到Page Rules，免费用户可以建立三条规则。

* 全站HTTPS化

1. 强制整个网站走HTTPS。
2. 301重定向

把不带www的根域名301重定向到带www的二级域名，这个看个人喜好。

1. 缓存整个网站

因为本博客是静态博客，所以可以设置CloudFlare缓存整个网站的静态文件。

## 结束语

本博客托管于GitLab。
如有问题请联系我[mail](chunxiaqiu13@gmail.com)  

`文本修订于2017-12-12`