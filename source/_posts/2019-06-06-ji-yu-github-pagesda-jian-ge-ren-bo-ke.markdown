---
layout: post
title: "基于github pages搭建个人博客"
date: 2019-06-06 16:18:17 +0800
comments: true
categories: 
---

博客作为一种技术总结以及交流的载体，已经成为大多数码农提升自我，帮助他人的必备平台之一。本文将总结一下搭建博客的一些流程。基于搭建便捷性角度出发，本文采用github + octopress搭建个人博客。

<!--more-->

## 环境配置
- 安装ruby
   - 安装ruby容易出现问题
   
   ```
   brew install ruby
   gem install bundler
   bundle install
   gem env home // 确保gem目录在用户目录而不是系统目录
   ```
   
- git软件
   - 配置ssh-key
      - 将本地公钥添加到github，具体位置为：个人头像->setting->ssh and gpg keys。
   - 创建git-pages
      - 注意新建的git-pages，必须为username.github.io。
- markdown软件
   - mac下推荐使用macdown软件，开源免费。
- octopress软件
   - 该软件可以认为是对Jekyll的二次封装，进一步方便我们撰写博客，这也是我使用此软件的原因。
   - 安装与使用
      - 安装
      
      ```
      git clone git://github.com/imathis/octopress.git octopress
      cd octopress
      bundle install
      rake install
      ```

      - 使用
      
      ```
      rake setup_github_pages 
      ```

      此命令将进行一系列初始化操作：
      
         - 询问github pages的仓库url。
         - 将imathis/octopress远程点的名称’origin’修改为’octopress’。
         - 加入你的github pages库作为默认的'origin'远程点。
         - 切换活动分支从主分支到源分支。
         - 根据你的库url配置你的博客url。
         - 在_deploy文件夹设置一个master分支用于部署。
      
      ```
      rake generate
      rake preview // 本地预览，http://localhost:4000
      rake deploy  // 发布静态网页内容到github主分支
      rake new_post["title"] // 添加新的博客
      rake new_page["title"] // 添加新的页面，例如tab
      ```

      如果调用上述命令，出现rake版本不匹配错误，可以通过添加bundle exec rake ***解决。
   - 目录结构
      - source目录为相关模版文件；
      - _deploy目录为根据source目录中相关模块，生成静态文件；
      - public目录为最终推送到github的文件列表，从_deploy拷贝而来。
   - 源代码提交
   
   ```
   git add .
   git commit -m "your message"
   git push origin source
   ```

   上述命令将博客开发源代码提交到github pages仓库source分支。
   
## 访问统计模块   
clustrmap可以统计网页访问量，并能够在地图显示访问者地理位置分布，访问趋势等。<br>
添加此模块到side需要：

- 在source/_includes/custom/asides/中新建clustrmaps.html文件，文件中添加对clustrmap的调用。

```
<script type="text/javascript" id="clustrmaps" src="//cdn.clustrmaps.com/map_v2.js?u=***"></script>
```

- 在_config.yml中修改关于asides的设置。

```
default_asides: [asides/recent_posts.html, asides/github.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html, custom/asides/clustrmaps.html]
```
  
## 评论模块
博客主流评论模块，包括，“Disqus”， “多说”等，但是前者是美国的评论系统，国内受限制，无法访问；后者，在国内曾经十分流行，但是目前网站已经停止提供服务。

因此，经过多方对比，在保证可用性的前提下，最终选择了commenthub评论系统。使用该系统，需要进行如下操作：

- 在[commenthub官网](https://commenthub.herokuapp.com)申请ID, 并填写博客地址进行绑定。
- 在_config.yml配置`commenthub_id`。
- 在_layouts/post.html中添加一下代码，显示评论模块。

```
{% if site.commenthub_id and page.comments == true %}
  <section>
    <h1>评论</h1>
    <div id="commenthub_thread" aria-live="polite"></div>
  </section>
{% endif %}                                                         
```

- 创建commenthub.html文件

```
{% comment %} Load script if commenthub comments are enabled and `page.comments` is either empty (index) or set to true {% endcomment %}
{% if site.commenthub_id and page.comments != false %}
<script type="text/javascript">
    var commenthub_id = '{{ site.commenthub_id }}';
    var commenthub_website = '{{ site.url }}';
    var commenthub_identifier = '{{ page.url }}';
    var commenthub_url = '{{ site.url }}{{ page.url }}';
    var commenthub_title = '{{ page.title }}';
    (function () {
        var dsq = document.createElement('script');
        dsq.type = 'text/javascript';
        dsq.async = true;
        dsq.src = 'https://commenthub.herokuapp.com/js/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    }());
</script>
{% endif %}
```

- 在_includes/after_foot.html中引入commenthub.html。

```
{% include commenthub.html %} 
```

# 参考文章
- [Add Gitalk to Your Jekyll Blog](https://aerolith.ink/2018/08/25/Gitalk/)
- [Commenthub —— 多说、网易云跟帖的替代品](http://www.majiang.life/blog/octopress-and-hexo-comments-widget/)
- [码农的救赎：使用Github Pages搭建博客](http://toughcoder.net/blog/2014/10/16/blogging-like-a-hacker-with-github-pages/)
- [octopress－blog](http://wiki.11ten.net/Knowledge-Management/octopress%EF%BC%8Dblog.html)

