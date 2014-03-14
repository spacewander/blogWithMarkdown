在virtualenv中正确地安装python包
=====

首先需要声明一下，我使用的是virtualenvwrapper， 如果你是用virtualenv，可能情况会
不一样。并且为了简明扼要，以下由virtualenvwrapper创建的vitual environment一概简
称为virtualenv，希望不会让你把它跟virtualenv程序弄混淆了。

昨天我遇到了一个麻烦：发现在virtualenv下找不到之前安好的包。一开始我还没有意识到
这可能是virtualenv的问题，而是这个包的问题。

这是一个flask插件，叫做flask-WTF的(起个好名字很重要)，当然这个插件是用来处理表单
认证的，估计是wrap the form的简称。然后呢，这个包自己有一个问题，就是在0.9的时候
，它突然改变了自己的架构，把一些部件移到另外的子模块里，其变动之大只有py2下的
urllib2跟py3下的urllib相比拟。结果是显然的，许多人毫无戒备地掉到了坑里。

举个例子，0.9版以前，所有的部件都可以在flask.ext.wtf下import进来。但是在0.9版之
后，所有的Field都需要从wtforms里引入。此外，还有一个Form的常用类，现在既可以从
flask.ext.wtf，也可以从wtforms下面import进来。且慢！wtforms里的那个Form是
flask.ext.wtf那个的Form的基类。如果你想要使用Form来添加安全验证的保障，必须使用
flask.ext.wtf那个！

情况就是这么糟糕，难怪有人会在github上相关的issue上调侃，它是不是改名成WTF了。

啰里啰嗦说了一大堆，事实还没有说到我的问题上。不过正为flask-wtf所苦的童鞋可以参
考我上面的内容。
比如oschina上的这篇[教程](http://www.oschina.net/translate/the-flask-mega-tutorial-part-iii-web-forms)就需要修改修改。

我的问题，嗯，其实是没有办法引入所有在virtualenv下安装的包，原因嘛……因为我根本就
没把它安装进virtualenv里！

发现这一点很容易，使用pip freeze或pip list列出当前环境下的安装的包。结果呢，我发
现我把包安装到全局空间里了。。。(╯°Д°)╯︵ ┻━┻

错误示范： `sudo [-E] pip install thieves`

正确示范： `pip install thieves`

为何捏？

先解释下sudo -E吧。以下内容摘自sudo的用户手册：

    -E          The -E (preserve environment) option indicates to the security
                policy that the user wishes to preserve their existing environ‐
                ment variables.  The security policy may return an error if the 
                -E option is specified and the user does not have permission to
                preserve the environment.

虽然提到了preserve environment variables，但是sudo -E不会让你成功地把包安装在虚拟环
境中。事实上，根本不能使用sudo！

当你使用sudo时，包会安装到/usr/local/lib/python2.7/dist-packages下，不管你是在
virtualenv中还是不在其中。因为你现在是想**以root的身份**给系统中所有的用户安装一个
python包。

如果不用sudo，假设你不在virtualenv中，包同样会安装在/usr/local/lib/python2.7/dist-packages
之下，只不过由于没有root权限，不一定能成功安装。

那么，假设你在virtualenv中，包就会安装在/home/me/.virtualenvs/myvirtualenv/local/lib/python2.7/dist-packages
中，注意跟前面的区别。

所以解决办法很简单：

1. 在对应的virtuallenv下面使用pip安装需要的包。
2. 不要使用sudo pip， 使用pip即可。

