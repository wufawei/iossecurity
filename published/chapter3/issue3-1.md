####文件系统查看工具
在iOS设备上可以安装[iExplorer](http://www.macroplant.com/iexplorer/), [iFunbox](http://www.i-funbox.com/cn/ifunboxmac/), [iTool](http://www.itools.cn/itoolsmacjiantibanxiazai)等工具，可以查看iOS应用的文件系统结构。


我们以iFunbox为例子，介绍下其用法。


####下载安装

从这里[下载](http://www.i-funbox.com/cn/ifunboxmac/)iFunbox最新的Mac版本，然后安装。

打开iFunbox，点击左边的User Applications，然后点击你想打开的应用的图标，比如选择陌陌。打开陌陌的文件目录的示意图如下所示：

![](https://farm4.staticflickr.com/3691/14074837820_98da877899_c.jpg)

选择某个应用，比如打开微信目录，到Libraray\WebChatPrivate\Host下面找到关于DNS IP相关的文件，然后在对应的文件上右键点击，会出现一个选项“Copy to Mac”，能够把对应的文件复制到Mac上，然后就可以利用Mac上的工具查看这些文件了。

如下图所示：

![](https://farm6.staticflickr.com/5118/14077092949_272dea12b9_b.jpg)


需要说明一下，我的iPhone4s运行的是iOS 7.1.1，没有越狱，上面图中显示Jailed有误；最上面那个iPad是越狱过的。

它会记录你使用过的WIFI，比如其中一个以WIFI名称开头的文件：

**7daysinn401.getdns2**

打开7daysinn401.getdns2文件，内容如下：

    [caextshort.weixin.qq.com]
    cacheSecs=1800
    ip=183.232.98.167
    time=1399025111
    [calong.weixin.qq.com]
    cacheSecs=1800
    ip=183.61.38.166
    time=1399025111
    [cashort.weixin.qq.com]
    cacheSecs=1800
    ip=183.232.98.167
    time=1399025111
    [clientip]
    cacheSecs=86400000
    ip=59.62.239.24
    time=1399025111
    ...
    
从这个文件夹下面，可以看出微信对DNS的IP是做了缓存处理的。从另一个角度来看，你也可以说。

类似的，我们还能拷贝出存有聊天记录的DB文件，然后利用SQLite3工具来分析，这个放到SQlite3相关工具的那一节介绍。


可以看到，使用iFunbox和iTools能够很方便的把iOS设备上的文件信息拷贝出来，即使设备没有越狱，也能够拷贝出应用目录下的所有文件。

***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)
