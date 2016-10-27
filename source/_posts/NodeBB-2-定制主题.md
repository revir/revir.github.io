---
title: 'NodeBB 2: 定制主题'
date:
tags:
    - Nodejs
    - NodeBB
    - V2MM
---

## skins
也许你只是想简单的更换按钮或字体的颜色， 那么你可以使用 NodeBB 提供的皮肤。
NodeBB 的前端基于 twitter 的 Bootstrap 开发，Bootstrap 强大的社区力量提供了很多漂亮的皮肤可以选择。
![Alt text](/img/post/nodebb2-skin.png)

## widgets
NodeBB 的主题可以包含很多 widgets area, widgets 其实是由一个个的功能模块组成，比如 recent posts, hot topics 等等，当然也可以自定义 HTML, 后台可以选择将其添加在某些页面上的某些定义好的区域，比如 主页的 footer, categories 页面的 sidebar 等等。
[V2MM] 就是通过这种方法在网站上添加 global footer 的。其中包含版权声明和友情链接等。
```html
<div class="text-center text-muted"  style="margin-top: 50px;">
    <div class="row">
        <span class="small">
            &copy; Copyright
            <strong>{config.siteTitle}</strong>
            <span> - <a href="https://github.com/revir/nodebb-theme-v2mm" target="_blank">theme v2mm</a></span>
            <span>- Built with Love v{config.version}</span>
        </span>
    </div>
    <div class="row">
        <em>
            Wake up, you need to make money.
        </em>
    </div>
    <div class="row">
        <a href="https://www.inog.site" target="_blank">INOG</a>
        ,
        <a href="http://blog.riverrun.xyz" target="_blank">River's blog</a>
        ,
        <a href="http://www.leyafo.com" target="_blank">Leyafo's blog</a>
        ,
        <a href="http://blog.cycleuser.org" target="_blank">Cycleuser</a>
    </div>
</div>
```
效果如下：
![Alt text](/img/post/nodebb2-footer.png)


## Theme
主题是 NodeBB 的插件，在 nodebb 的插件管理器中搜索是已 nodebb-theme 作为前缀的插件。其实它的实现方式完全和插件是一样，不同的是多了一个 theme.json 和 theme.less 的配置。

theme.json 定义主题名称，目录结构等。有趣的是还可以指定 baseTheme, 这样如果你的主题没有支持某些 templates, 将会 rollback 回 baseTheme。 默认的 baseTheme 是 persona。

Theme 的开发也要 plugin.json 来指定 hooks， scripts, libraries 等。请参考[开发文档](https://docs.nodebb.org/en/latest/themes/create.html#configuration)。

## V2MM Theme
[V2MM] 是我创建的一个自由职业者论坛，使用的是我自己创建的 [v2mm theme]， 已开源，欢迎加入 [V2MM], 一起讨论学习。


[V2MM]: http://v2mm.tech
[v2mm theme]: https://github.com/revir/nodebb-theme-v2mm


