
如果你直接从iOS设备上导出从App Store下载的应用的IPA包，你会发现其内容是加密过的。

App Store上的应用都使用了[FairPlay DRM](http://en.wikipedia.org/wiki/FairPlay)数字版权加密保护技术。

我们要对文件进行反汇编，而IPA都是加密的，哪怎么办呢？

可以使用Clutch工具。

不管应用如何加密，在其运行的时候，它总要解密，所以，Clutch等破解工具，就是把应用运行时的内存数据按照一定格式导出。

Clutch开源，代码在[这里](https://github.com/KJCracks/Clutch)，你也可以直接下载它的[Release程序](https://github.com/KJCracks/Clutch/releases)。



下载Clutch之后，利用前面介绍的iFunbox等工具把这个文件拷贝到越狱之后的iOS设备上的/usr/bin目录下。

然后，在Mac上打开命令行，输入命令：

    ssh root@192.168.0.101
    
   

然后输入秘密，就可以连上你的iOS设备了。越狱之后，默认秘密是**alpine**。

请在登录进去之后，输入命令**passwd**更改。

入图：
![](https://farm4.staticflickr.com/3696/14278568966_25319bf1db_b.jpg)

请注意，上面的ip应该替换成你的iOS设备的IP。

在命令行中输入Clutch看看，如下图：

![](https://farm3.staticflickr.com/2934/14298353251_ca4685f0f4_b.jpg)

选择你要破解的应用的名称，这里的名称是上图中输入Clutch之后显示那些名称。

比如：

    Clutch Airbnb


如下图所示：

![](https://farm3.staticflickr.com/2908/14115156547_8d5e488cca_b.jpg)



可以看到，成功破解应用，你可以把这个破解后的文件从iOS设备上拷贝到你的Mac上做后续的分析了。


###小结

本文简要介绍了Clutch的作用和用法，利用Clutch可以很方便的破解应用。



***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)
