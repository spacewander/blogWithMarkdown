chrome 插件 vimium 介绍
====


**vimium是一个chrome插件，用于在chrome中提供像vim这样的纯键盘的快捷操作方式。**

chrome webstore [链接](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb)

该插件是开源的，使用coffeescript创作。
[github地址](https://github.com/philc/vimium)

通过使用vimium，我们可以在浏览网页的时候也可以使用“键盘流”，把使用vim时的体验移植到浏览器上。
就我个人的体验而言，vimium虽然不能完全替代掉触摸板/鼠标，但是已经相当不错了（对一个键盘党而言哈）。

所以，在这里我想介绍一下vimium的一些命令，方便大家熟练地使用键盘浏览网页，从此抛弃长长的鼠标线。

vimium 命令详解
====

首先，vimium的命令是可以在"advanced option"这一项下进行自己的mapping。

其次，vimium的具体命令的意思可能会与同名的vim命令不同，毕竟两种命令是应用在不同的任务下的。

好，让我打开vimium的配置页，点开右侧的"Show available commands",
看看有哪些内置命令：

Navigating the page
----

    j,<c-e> : 向下滚动
    k,<c-y> : 向上滚动
    h       : 向左滚动
    l       : 向右滚动
    gg      : 回到顶部
    G       : 回到底部
    d       : 向下滚动一页
    u       : 向上滚动一页
    r       : 重新载入页面
    gs      : 显示网页源代码
    yy      : 复制当前网页url
    yf      : 复制链接url
    p       : 在当前页打开刚复制的url(注意不是简单的粘贴！)
    P       : 在新的一页打开刚复制的url(注意不是简单的粘贴！)
    i       : 进入插入模式
    f       : 在当前页打开链接
    F       : 在新的一页打开链接
    o       : 打开url,书签或历史记录
    O       : 打开url,书签或历史记录(在新的一页)
    T       : 在打开的标签页中搜索
    b       : 在当前页打开书签
    B       : 在新的一页打开书签
    gf      : 到页面上的下一个frame

Using find
----

    /       : 进入查找模式
    n       : 到下一个匹配位置
    N       : 到上一个匹配位置

Navigating history
----

    H       : 在浏览历史中倒退一步
    L       : 在浏览历史中前进一步

Manipulating tabs
----

    K,gt    : 到右边的tab
    J,gT    : 到左边的tab
    g0      : 到第一个tab
    g$      : 到最后一个tab
    t       : 打开新的一页
    yt      : 复制当前页
    x       : 关闭当前页
    X       : 重新打开关闭页面

Miscellaneous
----

    ?       : 帮助

That's All
====
