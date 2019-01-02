---
layout: post
title: "如何安装jekyll(只需1分钟)"
date: 2019-01-02 13:00 +0800
categories: tech
---

[![](http://rayxyz.github.io/assets/images/general/install-jekyll-in-a-minute.png)](http://rayxyz.github.io/assets/images/general/install-jekyll-in-a-minute.png)

# 操作系统
```
Ubuntu 16.06 LTS
```

# 安装ruby和ruby-dev
```
sudo apt-get install ruby
sudo apt-get install ruby-dev
```

# 开发安装jekyll
```
sudo gem install bundler jekyll
```

# 创建站点
```
sudo jekyll new jklpro
```

# 运行站点
```
bundle exec jekyll serve
```

## 运行时输出
```
Configuration file: /home/ray/workspace/jklpro/_config.yml
            Source: /home/ray/workspace/jklpro
       Destination: /home/ray/workspace/jklpro/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.393 seconds.
RubyDep: WARNING: Your Ruby is outdated/buggy.
RubyDep: WARNING: Your Ruby is: 2.3.0 (buggy). Recommendation: upgrade to 2.3.1.
RubyDep: WARNING: (To disable warnings, see:http://github.com/e2/ruby_dep/wiki/Disabling-warnings )
 Auto-regeneration: enabled for '/home/ray/workspace/jklpro'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
[2019-01-02 13:24:08] ERROR `/favicon.ico' not found.
```

此时，打开浏览器并输入http://127.0.0.1:4000/,就可以访问生成的站点了：
[![](http://rayxyz.github.io/assets/images/general/install-jekyll-in-a-minute-page-view.png)](http://rayxyz.github.io/assets/images/general/install-jekyll-in-a-minute-page-view.png)
