---
title: How To Use Hexo
date: 2022-10-30 12:40:26
cover: /img/hexo/hexo.png
toc: true
excerpt: This is an introduction for a very beginner about Hexo.
tags: Hexo
---

# Introduction

The combination of Hexo + GithubPages is an easier way to configure your own blog.  Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

# Steps

## Create a GitHub Repo

You should create a repo for your blog, and the repo name must be `<username>.github.io`.

## Init Hexo

Then, navigate to `<username>.github.io` directory, run the following command to init Hexo blog.

```bash
# cd <username>.github.io
hexo init
```

## Install Dependencies

Finally, run the following command to finalize installing procedures.

```bash
npm install
```

# Hexo Useful Commands

## Create a new post

Enter \<username\>.github.io directory, and execute the following command for new page creation.  If you haven't specify layout, hexo will use the default layout for new page creation. The default layout is configured in _config.yml file, the keyword is `default_layout`. For more info, please
see chapter [Writing](https://hexo.io/docs/writing.html).

``` bash
# hexo new [layout] <title>
$ hexo new "My New Post"
```

## Run server

If you want to preview your blog on your local machine, you can use following command to deploy your blog on your local machine.  For more info, please see chapter [Server](https://hexo.io/docs/server.html).

``` bash
$ hexo server
```

## Generate static files

Before you publish your blog to github pages, you should generate static files locally.  If you use the github pages's theme, you only need to configure wanted theme in your `_config.yml` file, and github will gengerate all static files before deploying. For more info, please see chapter [Generating](https://hexo.io/docs/generating.html).

``` bash
$ hexo generate
```

## Deploy to Remote Sites

After generating all static files, you can deploy your blog on GitHub pages. This command would create a `master` branch in your blog repo. So your github pages should be based on `master` branch. You can change the github pages' branch in `Settings->Pages->Branch`.

![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/change_default_branch.png)

``` bash
$ hexo deploy
```

For more info, please see chapter [Deployment](https://hexo.io/docs/one-command-deployment.html).

# Themes

## Install Theme
I'd like to use `hexo-theme-icarus` theme. User should execute the following commands to configure icarus theme.

[hexo-theme-icarus](https://github.com/ppoffice/hexo-theme-icarus)

```bash
npm install hexo-theme-icarus
hexo config theme icarus
```
Hexo provides numerous themes, please see [Hexo-Themes](https://hexo.io/themes/) for more details.

## Change Your Personnel Info

User should change the personnel info in `_config.icarus.yml` file.

## Notes

All blog data are listed in `source/` directory. My `source` directory hierarchy is as follows:

```
» tree source     
source
├── _posts
│   ├── How-To-Use-Hexo.md
│   └── hello-world.md
└── img
    ├── alipay-qrcode.jpg
    ├── logo.png
    ├── logo.svg
    └── wechat-qrcode.jpg

2 directories, 6 files
```

If you want to change the default logo `logo.svg`, you should put your own `logo.svg` in `source/img/logo.svg`. Besides, Mac user can use `imagemagick` tool for png to svg conversion. For more details, you can see [imagemagick-homepage](https://www.imagemagick.org/script/index.php).  After install `imagemagick` tool, you can execute the following commands to convert.

```bash
convert logo.png logo.svg
```

## Add "Read More" tag for your posts
There are two ways to add this tag. 
* First, you can add `excerpt` tag in your post, and add some introductions for posts.

```yaml
title: New Article
date: 2022-11-12
excerpt: Introductions
---
# Contents ...
```

* add `<!-- more -->` line after introductions in your posts.

# References

* [PicGo-Configuration](https://ji2xpro.github.io/73d224e9/)
* [Using-ssh-instead-of-http-in-config.yml-file](https://github.com/hexojs/hexo/issues/4757)
* [Hexo-Doc](https://hexo.io/zh-cn/docs/) 
* [Hexo-theme-icarus](https://github.com/ppoffice/hexo-theme-icarus)
* [Hexo-theme-icarus-doc](https://ppoffice.github.io/hexo-theme-icarus/)