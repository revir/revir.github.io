---
title: 'NodeBB 1: 一个现代化的论坛'
date: 2016-10-20 16:14:18
tags:
    - Nodejs
    - NodeBB
    - V2MM
---

## NodeBB：一个现代化的论坛

最近对论坛感兴趣，准备学习一下，网上的开源论坛无数，为什么选择 [NodeBB] 呢？ 因为被它神速的单页面设计惊艳到了，它有完善的功能模块，可以用作 CMS，博客，论坛，甚至电子商务网站；它使用了 websocket 实时通信，可以让用户分组建群聊天；它的模块化设计，从主题到功能插件均可以自定义，支持 theme, widget, plugin 三种形式的扩展，社区已经有了很多功能强大的扩展； 它有比较完善的 wiki 和颇受欢迎的社区关注度， 从它开始着手学习再适合不过了。

* [github]
* [文档]
* [demo]

贴张图感受一下：
[![NodeBB demo](https://github.com/revir/nodebb-theme-v2mm/raw/master/screenshot.png)](http://v2mm.tech)

## 环境和依赖
[NodeBB] 是用 Node.js 开发的，显然需要 Node 环境，推荐使用 nvm 安装 node， 官网有详细[文档]。
[NodeBB] 同时支持 Redis 或 MongodDB 作为数据库。不同于某些网络应用仅使用 Redis 作缓存， NodeBB 是将数据全部存储在 Redis 里的，如果你的服务器内存够大，推荐使用 Redis。 当然如果你像我一样只是购买的一个最低配置的 VPS, 那还是推荐 mongodb. `./nodebb setup` 可以选择。

## 安装和启动
[NodeBB] 的安装和启动方式见[文档]，也可以用 node-inspector 调试，见详细的[调试教程]。这货的安装使用 npm install 一下就搞定了， 然后使用  `./nodebb setup`  或直接更改 config 文件配置自己的 database， 不要太简单。
本地调试就用 `./nodebb dev` 启动， 这样可以只启动一个实例，并且有调试日志。

[NodeBB] 默认运行在 4567 端口上， 如果要监听 80 端口， 推荐使用 Nginx 作反向代理， 见[教程:  Nginx作代理](https://docs.nodebb.org/en/latest/configuring/proxies/nginx.html). 或者在防火墙上设置转发规则，如 iptables:

    sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 4567



## 编写一个插件
[NodeBB] 的插件都是通过一个个的 [HOOKS] 运行的，通过 [ACP] 的 plugins 管理页面安装几个插件，插件其实就安装在 node_modules 目录下，看看其他插件的写法，其实很简单，开始着手写一个。
首先在 node_modules 下创建一个目录， 然后再创建一个 plugin.json,  其中定义插件需要的 templates, static resources 等等，关键是 [HOOKS] 指定钩子。比如:
```
...
{ "hook": "static:app.load", "method": "init" }
...
```
这样在 plugin.json 中指定的 library.js 里的 init 函数就可以在程序启动的时候运行你的插件初始化函数了。
目前我的插件还在构思开发中，开发完成后必定开源......
HOOKS 的函数有多种，包括 filters, actions 和 static, 各司其职，务必阅读[插件开发指南], 可恨的是每个函数接口并没有清晰的定义，只能直接读源代码， 通过上下文来判断。
如果你也有兴趣开发，我们一同进步吧！

## 自由职业者社区: V2MM

需要学习更多的 NodeBB 开发知识，欢迎加入 [v2mm]。[v2mm] 是我基于 NodeBB 搭建的一个自由职业者社区，旨在提供给追求自由的 Geek 们一个自由讨论，互相学习与分享的地方。[v2mm] 的成长离不开 [NodeBB] 社区的贡献，因此 v2mm 制作的主题和插件都将开源，欢迎加入我们！

无论你是程序猿，还是设计狮，抑或是产品经理，
如果你做的是自由职业，
每天睡到自然醒，代码撸到凌晨一两点，
或者你只是在远程办公，
一台电脑就是你叱诧风云的利器，
你只需要有一颗追求自由的心，
那么我们就是自由职业者！


[v2mm]: http://v2mm.tech
[github]: https://github.com/NodeBB/NodeBB
[NodeBB]: http://nodebb.org
[demo]: http://v2mm.tech
[文档]: https://docs.nodebb.org/en/latest/
[调试教程]: https://docs.nodebb.org/en/latest/resources.html#debugging
[HOOKS]: https://github.com/NodeBB/NodeBB/wiki/Hooks
[ACP]: https://docs.nodebb.org/en/latest/admin/index.html
[插件开发指南]: https://docs.nodebb.org/en/latest/plugins/create.html

