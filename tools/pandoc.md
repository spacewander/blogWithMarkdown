#pandoc

Pandoc是一个神奇的软件。
它包括一个Haskell库，
可以将一种标记格式转换成另一种标记格式。
还有一个命令行接口来调用这个库。

##目前Pandoc支持的标记类型包括：

* 输入：
	
	1. markdown
	2. reStructedText
	3. HTML
	4. LaTex
	5. DocBook
	6. etc...

* 输出:

	1. markdown
	2. reStructedText
	3. HTML
	4. LaTex
	5. DocBook
	6. ODT
	7. Emacs OrgMode
	8. RTF
	9. etc...

如果系统上安装了LaTex，那么还可以输出PDF。

详细地调用方式可以查看man pandoc.
或者到Pandoc的[官网](http://johnmacfarlane.net/pandoc/README.html).

##为什么说Pandoc是神奇的软件呢？

首先，pandoc居然是用haskell写的...

这不是开玩笑啦。
我对这一个神奇的函数式编程语言一直久仰大名，
不过却怀着“虽然好玩但是不太实用的样子”的偏见。
直到我遇到这个软件，才知道原来haskell还能写出这么神奇的东西。

其次，pandoc支持的文件类型之多，超出我的想象。
嗯，几乎我能叫得出名字的轻量级标记语言它都支持了，还包括我叫不出名字的。
可以说是，文件格式转换的集大成者。

另外，按照官网上的说法，pandoc在各种标记文档间转换自如，
不是靠简单的正则等价替换，而是使用了不同的reader来生成native表示，
然后再使用不同的writer来把native表示转换成不同的标记文档。

使用这种架构，可以通过增加reader和writer来支持一门新的标记格式。

这意味着，这个软件包括了一个插件系统。

一般来说，如果一个软件包括了一组快捷键，那么它会比同类软件效率更高。
但是如果一个软件包括了一个插件系统……嗯，就意味着它已经不是一个单纯的软件，
更应该称之为“平台”了。
同类的软件，就如士多比超市，已经无法比拟。

在官网的user guide还讲到，pandoc可以使用著名的“嵌入式语言”*lua*进行拓展。
（pandoc自身已经包括了一个lua解释器，所以无须单独安装lua）
具体的使用方式可以查看**Pandoc User`s Guide#Custom writers**[一节](http://johnmacfarlane.net/pandoc/README.html#custom-writers)。

最后，不能不提到的是，pandoc是在GPL协议下发布的开源软件哦！

现在就来尝试吧！

	pandoc -f markdown -t rst pandoc.md > pandoc.rst

