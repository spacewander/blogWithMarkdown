man攻略 read,write,post
=====

正如题目所言，这篇文章是讲UNIX中的man命令的。
man命令是manual的缩写，通过这个命令可以查阅UNIX系统手册。

可能有人已经注意到，讲UNIX的书，在提到某些命令或系统调用时，总会在其后添加一个数字序号。
这个序号表示的是这个命令(或调用)在UNIX系统手册的第几部分。

在过去，UNIX用户手册的确是一本书，书里分几个章节。后来不再随系统附带说明手册，改把说明手册附到系统里，使用工具来查阅对应的条目。
即使现在同一个章节内的条目已经不一定放到一块，UNIX用户手册依然照过去的样子分成下面几章：

1. 通用命令
2. 系统调用
3. 库函数，主要是C标准库函数
4. 特殊文件比如设备，和驱动等等
5. 文件格式和约定
6. 游戏和屏保(你没看错，的确会有这种东西)
7. 杂项
8. 系统管理员命令和守护进程。

居然不从0开始...许多书上会讲，UNIX用户手册一共分八个部分(包括APUE也是这么说)。
但是，实际上有些系统(比如Linux和Mac OS X)就不太传统，在基本的八个部分上增加了新
的章节。
具体情形比较复杂，可以参照[维基百科](http://en.wikipedia.org/wiki/Man_page#Manual_sections)条目下的Manual sections。

在大部分情况下，不需要指定特定的章节，只需简单地使用`man xxx`。但是有些情况下需
要加以区分，比如`man read`，如果是想要查系统调用的那个，需要显式指明`man 2 read`。
当然你也可以指定man page所在的路径，如`man ./xxx/yyy/zzz.1 `。

Read
-----

在man里打开一个文件后，可以通过d或u来往下翻半页或者往上翻半页。如果想要翻一整页，使用z向下，或者w向上。
如果想要搜索某个词，使用/进入搜索模式，用n到下一个匹配处，用N到上一个匹配处。
如果想要直达文件头/尾，使用g/G。详情可以使用h来打开帮助页面。总之，如果你使用过Vim，就不会感到陌生。

其实man页面之所以可以这样浏览，是因为它调用了less作为自己的默认浏览程序。
你可以使用`man xxx -P yyy`来指定别的程序打开man页面，比如`man xxx -P cat`。

write
-----

既然已经讲完了read，是时候讲讲write。

假如你想为自己的程序写一个man page的说明，应该怎样？

方法大概有三：

1. 找一个类似的man页面，复制然后定制。这个就不用详细说了。不过你需要看看下面的manual路径，方便你找到所需的页面。

2. 参照man的格式(groff)，自己写一个。额，不提倡。所以这里就同不说了。
不过假如你真想这么做，推荐[这篇文章](http://www.schweikhardt.net/man_page_howto.html)

3. 使用文档格式转换工具，把别的格式的文本文件转化成man page。这是本人推荐的方式
。比如[help2man](http://www.gnu.org/software/help2man/)或者[ronn](https://github.com/rtomayko/ronn)，还有著名的pandoc。

如果想用pandoc，先看看这个demo：<http://johnmacfarlane.net/pandoc/demos.html> 里
面关于生成man page的那一条。

如果想要用ronn，那么就往下看吧：

### ronn

安装：
    
    gem install ronn
    # ronn 需要安装ruby和gem

使用：

    ronn xx.x.ronn

ronn需要用ronn格式的文本来转化成man page。
Don`t Panic. ronn是一种拓展的markdown文法，懂markdown的人看一下就知道如何编写ronn格式文本了。
其实pandoc也不能直接将普通的markdown转化成man page，它也需要使用它自己的markdown拓展。

废话不说了……如果想了解ronn文法的话，看<https://github.com/rtomayko/ronn>下man文件夹里的几个示范吧。
一如普通的markdown文法，就是生成的man page的样式跟markdown对应的html不一样而已。博主只把两个示例看了半遍，就能写成所需的ronn文本了。

放上自己的demo，地址： <https://gist.github.com/spacewander/9662830>

---

这里就不讲help2man怎么用了……感兴趣的童鞋到对应的项目主页看看吧。

post
------

假如你已经写好了一个man page，那么怎么样才能让man命令找到你的man page呢？
让我们先从man的查找方式说起：

### manual路径

man到底是怎么查找到这些manual呢？

man会在目标路径里查找。
这个可以通过`man -w`(或`man --path`，取决于你的喜好)来获取。
如果`-w`选项后面没有加参数，就输出系统定义的搜索manual路径；否则输出参数所在的man页面所在的文件位置。

### 发布你的man page

既然能够获知man的搜索路径，那么可以考虑直接复制文件进去……当然肯定不只有这种“不负责任”的办法。

使用`man man`，然后`\SEARCH PATH FOR MANUAL PAGES`，就能找到一段关于manpath的介绍。
由于本文已经显得比较冗长，这里就不复制对应的内容了。
简而言之，就是可以修改环境变量中的`$MANPATH`来改变man的搜索路径。
这样一来，把你的文件所在目录加入到搜索路径就OK了。
