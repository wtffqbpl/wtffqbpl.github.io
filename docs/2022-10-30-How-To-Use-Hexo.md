---
title: How To Use Hexo
---

# Introduction

The combination of Hexo + GithubPages is easy way to configure your own blog.

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

# Hexo Useful Commands

## Create a new post

Enter \<username\>.github.io directory, and execute the following command for new page creation.
If you haven't specify layout, hexo will use the default layout for new page creation. The default
layout is configured in _config.yml file, the keyword is `default_layout`. For more info, please
see chapter [Writing](https://hexo.io/docs/writing.html).

``` bash
# hexo new [layout] <title>
$ hexo new "My New Post"
```

## Run server

If you want to preview your blog on your local machine, you can use following command to deploy
your blog on your local machine.  For more info, please see chapter
[Server](https://hexo.io/docs/server.html).

``` bash
$ hexo server
```

## Generate static files

Before you publish your blog to github pages, you should generate static files locally.
If you use the github pages's theme, you only need to configure wanted theme in your
`_config.yml` file, and github will gengerate all static files before deploying.
For more info, please see chapter [Generating](https://hexo.io/docs/generating.html).

``` bash
$ hexo generate
```

### Deploy to Remote Sites

After generating all static files, you can deploy your blog on github pages. This command
would create a `master` branch in your blog repo. So your github pages should be based on
`master` branch. You can change the github pages' branch in `Settings->Pages->Branch`.

![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/branch_configuration.png)

``` bash
$ hexo deploy
```

For more info, please see chapter [Deployment](https://hexo.io/docs/one-command-deployment.html).

### References

* [PicGo-Configuration](https://ji2xpro.github.io/73d224e9/)
* [Using-ssh-instead-of-http-in-config.yml-file](https://github.com/hexojs/hexo/issues/4757)
* [Hexo-Doc](https://hexo.io/zh-cn/docs/) 