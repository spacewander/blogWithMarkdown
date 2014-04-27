Why All My MPI Process has the rank 0?
=====

嗯，当我开始写MPI程序的时候，也遇到了这个问题。

## 背景知识

先介绍一下背景。

MPI全称是message passing interface，即信息传递接口。它提供让相关进程之间进行通信，同步等操作的API，可以说是并行计算居家出游必备的基础库。

MPI的编程方式，是“一处代码，多处执行”。编写过多线程的人应该能够理解，就是同样的代码，不同的进程执行不同的路径。

那么怎么区分不同的进程呢？

MPI有几个常用的API，其中有一个叫做`MPI_Comm_rank`, 它返回调用它的进程的具体进程号。比如我启动了10个进程，然后每个进程的这个调用会返回0-9对应的进程编号。这样它们就知道谁是老大，谁是老二，谁是老九……于是我们可以很确定地下命令，让老大去买菜，老二到老五洗中午的碗碟，老六到老九准备做饭。

要想运行MPI并行程序，使用普通的编译器是不行的，需要使用特殊的mpi编译器。其实所谓的mpi编译器，只是普通的编译器外加了个套件。比如mpicxx，就是在g++外面套了个mpi的壳。而且除了编译器要用特殊的编译器之外，运行时还要用特殊的程序，比如mpiexec和mpirun。

这里澄清一下，虽然mpiexec和mpirun除了前缀其他各不相同，但是它们不是两兄弟，只是一个程序的不同说法罢了……
具体的运行方法，是`mpirun -n x ./myProgram`，其中x是要启动的进程数。这时候，如果你使用系统监视器或者终端下的`top`命令，应该能够看到x个名字一模一样的进程正嘿哟嘿哟地运行着。

前面讲到的`MPI_Comm_rank`调用，返回的值为0到x-1中的一个值，取决于对应的进程是老大还是老x。

背景知识介绍完毕。如果想要知道更多，请移步到维基页面[信息传递接口](http://zh.wikipedia.org/wiki/%E8%A8%8A%E6%81%AF%E5%82%B3%E9%81%9E%E4%BB%8B%E9%9D%A2)；
想要知道更更多，请移步到英文维基页面[Message Passing Interface](http://en.wikipedia.org/wiki/Message_Passing_Interface)。

## 问题

好了，是时候交代我的问题了。

**无论我指定了多少个进程，所有的进程都会声称自己的进程号为0.**

没错，`MPI_Comm_rank`调用不灵了。

## debug

在排除了我自己在代码中犯错的情况后，我果断可以求助于万能的互联网。

搜索了一下才知道，原来网上也有很多人遇到了跟我一样的问题：
> Why All My MPI Process has the rank 0?

OK, 这样就简单多了^_^。。。

Wait!怎么答案有点不靠谱？>_<

在爆栈网上有一个相关的问题，有人回答：
> 这是openmpi的错！卸openmpi保平安！卸掉装mpich就好了！
不过接下来有人回答：
> 我卸掉mpich就正常了！现在哥用的是openmpi!
你们……

（这里解释一下：openmpi和mpich是mpi的两个开源实现，同样也是使用最广泛的两个实现。）

## 转机

嗯，于是我丢下了不靠谱的答案，继续在茫茫的信息大海中捞取有用的内容。

然后我看到了：（原文为英文，但是具体内容已经忘了，所以这里复述其大意）
> 为什么我的MPI程序中所有的进程都返回rank 0呢？
> 你检查一下mpic++和mpiexec具体链接到哪个程序上？
> 哦，原来...

**一言惊醒梦中人！**

于是乎用`which`来查看`mpicxx`和`mpiexec`，结果大出我所料，`mpicxx`居然有`openmpi`和`mpich`两个版本。
同样的事情也发生在`mpiexec`上。

有趣的是，这两个版本的后缀是不同的。`mpicxx`链接到的是`mpicxx.mpich`和`mpicxx.openmpi`，`mpiexec`也一样。

那么，名字上的冲突会导致什么问题呢？

## 答案

根据这篇[mpich FAQ](https://wiki.mpich.org/mpich/index.php/Frequently_Asked_Questions)，
可以看到mpich官方对于**All my processes get rank 0**这个问题的解答。

鉴于内容较多，且大部分内容不在“人话”的领域内，我就只摘抄结论如下：
> So, in some sense, mpiexec or srun is just a user interface for you to talk in the appropriate PMI wire protocol. 
> If you have a mismatch, the MPI process will not be able to detect their rank, the job size, etc.,
> so all processes think they are rank 0.

所以，如果调用的`mpiexec`和`mpirun`不是同一个版本的，就会产生**所有进程都返回rank0**这种情况。

## 解决

解决方法很简单，在`openmpi`和`mpich`中选择一个即可。
不管怎样，在编译和运行时，最好都添加上后缀，比如使用`mpicxx.mpich`编译，那么运行就用`mpiexec.mpich`。

虽然最后还是用卸掉其中一个解决了问题……不过至少现在知道了其中的原理，而不是人云亦云地敷衍了事。

