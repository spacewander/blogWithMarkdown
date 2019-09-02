如何正确地在Vim里记录折叠等视图？
=====

如果你用Vim的折叠命令手工折叠/打开了若干处，然后你希望下次打开的时候也能看到这
些折叠，你应该怎么做？

在一个月之前，我开始写一些javascript代码，然后需要折叠一些跟我的代码无关的内容，
来突出自己写的逻辑。

然后我惊奇地发现，Vim居然不会保存我制作的折叠！这一点出乎我的所料，因为无论是IDE
，还是我以前使用过的文本编辑器(Notepad++, Sublime 2)，都会提供记忆折叠的特性。不
过Vim作为编辑器之神，根据以前我折腾Vim的经验，这个功能很可能是有的，只是没有被设
置出来罢了。

于是上网找。很快就找到了这条：

    :::vim
    au BufWinEnter * silent loadview
    au BufWinLeave * silent mkview

这段Vimscript代码很直白，就是在BufWinEnter的时候执行自动命令加载对应的视图，在
BufWinLeave的时候执行自动命令制作对应的视图。视图里就包含了文本的折叠情况。把它
拷贝到vimrc里就好了。

现在，我再也不用在反复地手工折叠不相关的代码了，Vim能够记忆每次会话后代码的折叠
情况，并自动为我加载。

不过!
-----

要是这样，这篇文章也就到此结束了。

可惜，世事总不遂人愿。很快我就遇到了问题。

1. 当我打开一个未命名文件时，会显示一个“E32 没有文件名”的错误……
2. 如果前面那个比较正常，那么还有一个让人摸不着头脑：当我使用帮助命令打开帮助文件时，
也会显示E32。
3. 还有一个问题，当我删掉某个文件，然后又重新复制了一个同名文件后，会产生视图加
载错误。

好吧，第3个算不上什么问题，第1个可以接受。但是第2个就让我百思不得其解了。我特意
使用expand('%')命令确认一下，帮助文件是显然有名字的。

不过你既然都说了，错误是没有文件名。那么我就修改一下，如果没有文件名，就不调用
loadview和mkview。于是代码改成这样：

    :::vim
    if expand('%') !=? ''
        au BufWinEnter * silent loadview
        au BufWinLeave * silent mkview
    endif

代码不需要解释吧？就是加了个判断而已。可惜这个代码不顶用。之前提出的问题1,2都没
有解决。我以为是没有调用，加了个echo语句上去，结果发现无论如何，if里面的内容都会
被执行，而我在对应的未命名文件用命令行模式执行这段代码，if里面的内容不会执行。(
当然，在帮助文件下显然会执行这段代码。)

嗯，那好吧。于是改成代码2：

    :::vim
    if expand('%') !=? '' && &filetype !=? 'help'
        au BufWinEnter * silent loadview
        au BufWinLeave * silent mkview
    endif

结果呢，还是不行。一开始我以为是`expand('%')`和`filetype`的判断那里出问题，于是
换成别的API。但是，无论我怎么折腾，那个烦人的E32还是一再！反复！出现！

看来我只能忍了？不过我很快想到，会不会是执行顺序的问题呢？会不会是BufWinEnter和
BufWinLeave那里的问题？

于是，我又走上阅读Vim文档的道路。在反复通读了`autocmd.txt`之后，我终于大概弄清楚
这些Vim事件的执行顺序。

    BufReadPre -> 获得文件名及大小 -> BufReadPost -> BufRead -> BufWinEnter ->
    BufEnter -> BufWrite -> BufWritePre -> 写入磁盘 -> BufWritePost -> 
    BufLeave -> BufWinLeave

    BufLeave <-> BufEnter

但即使如此，我反复地把那两个BufXXX事件换成其他的BufXXX事件，要不无法解决E32问题
，要不可以避免E32，却无法正常加载视图。

对策
-----

好吧，在忍受了一段日子后，我突然想到了一个比较蠢，但是管用的办法，就是硬编码。我
把两个视图命令写出一个函数，然后制定一个可以调用它们的白名单类型，比如：

    :::vim
    function! SetView()
        au BufWinEnter * silent loadview
        au BufWinLeave * silent mkview
    endfunction

    augroup setFileView
        autocmd!
        au FileType python :call SetView()<cr>
        au FileType c :call SetView()<cr>
        ...

大概就是这么挫。不过至少在使用帮助文件时，再也不会有E32了。

解法
-----

就这样将就了一段时间。有一次我在查找有关执行外部命令的资料时，突然看到了一个
silent!命令，它可以使得命令的输出完全屏蔽掉，包括错误。(Vimscript里没有异常的概
念，只有错误，而且没有捕获错误的机制)

之前大家都应该知道一个silent命令，这个silent!是它的加强版，连错误都保持沉默。

这不是我想要的吗！！！于是，代码最终变成这个样子：

    :::vim
    au BufWinEnter * silent! loadview
    au BufWinLeave * silent! mkview

问题终于解决了！虽然现在依旧会有错误抛出，不过这已经跟我无关了。眼不见为净嘛。

后记
----

[@Cloudprogram](https://github.com/Cloudprogram) 提供了个更好的方法来解决 E32 问题。

参考；https://vi.stackovernet.com/cn/q/4214

Switching to ?* as the regex will no longer match empty file names. I actually recommend you switch to the following, which is a corresponding block of .vimrc that has been tweaked to suppress many of the errors you are likely to encounter when following the referenced advice.

```
augroup AutoSaveFolds
     autocmd!
     " view files are about 500 bytes
     " bufleave but not bufwinleave captures closing 2nd tab
     " nested is needed by bufwrite* (if triggered via other autocmd)
     autocmd BufWinLeave,BufLeave,BufWritePost ?* nested silent! mkview!
     autocmd BufWinEnter ?* silent loadview
augroup end
```

The BufWritePost event (+ nested) can be left out if you prefer, though you will experience no penalty for leaving it in.

Furthermore, I use

```
set viewoptions=folds,cursor
set sessionoptions=folds
```
