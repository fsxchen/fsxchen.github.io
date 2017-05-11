---
title: Pelican和Github建博客
date: 2015-11-19 10:42
category:
	- Misc
tags:
	- Pelican
---

# 使用Pelican和Github来搭建博客系统

## 关于博客
之前所有的东西都记录在了印象笔记上，可以没有免费的markdown的插件，印象笔记对于记录一些东西来说还不错,不过对于写些东西,感觉还需要一些额外的东西.
后来发现这套解决方案还是不错滴！用atom来编辑，下载一些能够预览markdown的插件效果还是蛮不错滴，同步在github上，在哪都可以编辑修改。

## 关于github Page
[github Page官方地址](https://pages.github.com/)
## 关于Pelican

具体请见[Pelican文档](http://docs.getpelican.com/en/3.6.3/)

## Pelican的主题
可以很方便的更换主题，只需在配置文件里面简单的修改就好了
## Pelican的插件
Pelican的插件有很多
### LaTex支持
LaTex主要是比较方便的来写数学公式
首先是配置上, 需要加上render_math的插件：
其实用的就是`MathJax.js`

```sh
PLUGIN_PATHS = ['pelican-plugins']
PLUGINS = ['render_math', 'sitemap', 'autopages', 'subcategory' ]
```
接下来比较重要的,也就是比较核心的操作就是,在主题`templates/bash.html`下的head标签之前加上

```html
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
    "HTML-CSS": {
        styles: {
            ".MathJax .mo, .MathJax .mi": {color: "black ! important"}}
        },
        tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']],processEscapes: true}
    });
    </script>
    <script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML"></script>

</head>
```

$$E=mc^2$$

Latex数学符号清参见[Latex数学公式](http://web.ift.uib.no/Teori/KURS/WRK/TeX/symALL.html)

### 评论的支持
静态博客要支持评论,可以使用[disqus](https://disqus.com).
第一步,你只需要注册一个disqus帐.
第二部,需要设置`DISQUS_SITENAME`
```
SITEURL = 'http://fsxchen.github.io'

DISQUS_SITENAME = u"coucou-blog"
RELATIVE_URLS = False
```
搞定

## 使用Atom编辑博客
### 图片上传
写博客,必须能上传图片嘛,图片得在atom这个编辑器上下功夫,原先的模块,用的是以下模块:
`markclip` `qiniu-puloader` `markdown-assistant`,本来后面两个应该是可以的,但是在我的大Ubuntu下就是无法使用.所有在github上找了不少,后来把这三个的代码相互改了改,就可以用了,具体办法就是在`markclip`的配置中,增加一个七牛的配置.


## Tips

用下面的这个函数，一条命令就搞定了。当然需要fabirc以及`fabfile.py`
只需要执行`fab public`

```
# @hosts(production)
def publish():
    """Publish to production via rsync"""
    local('git add .')
    local('git commit)
    local('git push -f origin master')

```


自动同步`output`下的到`gh-pages`,可以编辑`.git/pooks/post-commit:`

```bash
chmod a+x post-commit
pelican content -o output -s pelicanconf.py && ghp-import output && git push -f origin gh-pages:master
```
