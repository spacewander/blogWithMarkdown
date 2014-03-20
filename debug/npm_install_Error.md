NPM Install : Error: No compatible version found
=====

前一段时间，我打算安装npm来玩玩。但是，我惊讶地发现，无论下什么包，都会有同样的
错误： Error: No compatible version found. 即使我直接指定了版本号，还是下不了对
应的包，所以应该不是版本号的问题。

作为墙内的子民，第一个想到的解决方案大概会是被墙的问题吧。不过我可以肯定地排除被
墙的可能，因为在被墙的情况下，直接连<https://registry.npmjs.org>都登不上。所以我
才特意战略转移到墙比较矮的网络环境(图书馆的教育网)下。现在总算可以连上官网了，但
是还是下不了对应的包，真是郁闷啊。

还是老办法，Google之，到StackOverflow看看。然后我找到了许多方法，不过大部分都未
能奏效。但是幸运的是，至少有一个是奏效的了。

解决方法在这里
<http://stackoverflow.com/questions/18562234/npm-install-express-error-no-compatible-version-found>

这个解决方法很奇妙吧，发行版源里的npm版本太旧了，添加一个新源，下最新版的npm就能
解决问题了。

现在我用的是npm 1.3.24， 总算可以成功地安装各种好玩的包啦。
