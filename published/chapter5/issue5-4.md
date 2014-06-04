iNalyzer允许我们查看类信息，执行运行时分析和其他一些事情。基本上它把解密应用、导出类信息这些事情自动化了，并且更好的展示了出来。我们也可以像Cycript那样挂钩运行的进程。iNalyzer由[AppSec Labs][1]开发和维护，它的官方地址在[这][2]。iNalyzer同时也已经开源了，gitub地址在[这][3]。

在用iNalyzer之前，有些依赖的软件需要先安装。请确保[Graphviz][4] 和[Doxygen][5]已经安装了，因为没有这2个工具，iNalyzer不会正常工作。并且，请注意，
我在Mac OS X Mountain Lion 10.8.4上做的测试，但是我们用最新版本的Graphviz的时候它经常会挂起（hang）。因此，我下载了Graphviz的一个较老的版本（v 2.30.1）
，并且这个老版本工作正常。你可以在[这][6]找到Graphviz for Mac的老版本。

第一步就是在你的iOS 设备上安装iNalyzer。先到Cydia->管理, 确保源http://appsec-labs.com/cydia/被成功添加，如下图。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat1.png)

然后到Cydia的搜索，搜索 iNalyzer。根据你现在设备上正在运行的iOS版本，选择对应版本的iNalyzer。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat2.png)

如你所见，我已经把iNalyzer安装好了。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat3.png) 

现在ssh进入设备，，然后转到iNalyzer应用所在的目录。iNalyzer安装在/Applications目录，因为它需要以root用户权限运行。如果你不了解这个概念，请确保你读过本系列
前面的文章。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat4.png)


输入./iNalyzer启动iNalyzer。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat5.png) 


现在如果你到主屏幕，然后看看iNalyzer应用图标，你会看到有个提醒数字。这表明这个应用 可以通过web接口访问，然后这个提醒数字就代表的是端口号。
如果你再次运行 ./iNalyzer，那么iNalyzer就会停止。因此，请确保记得./iNalyer是开启还是关闭这个应用。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat6.png) 

现在你可以找到你的设备的IP地址，然后用ip:port的方式在浏览器上打开。这里端口是5544,IP地址是10.0.1.23.因此url地址是http://10.0.1.23:5544/。一旦你打开这个页面，
你会看到如下图的界面。你可以选择一个应用，然后iNalyzer就会准备一个zip文件，然后下载到你的系统上以便分析。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat7.png)  


不过，我在这行这一步的时候却遇到一些问题。因此，我们将使用一个替代方法来完成这一步。首先确保iNalyer正在运行。然后转到iNalyer的目录下，然后不带任何参数的运行
iNalyer5。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat8.png)  

现在你可以看到一系列可以用来分析的应用。这里我们选择Defcon应用来分析。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat9.png)  


你可以看到iNalyer已经开始工作。它首先解密应用，找出对应的类信息和其它一些信息。如下图所示，一旦iNalyzer完成它的工作，它就会创建一个ipa文件，然后把它
保存到下图高亮的地址。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat10.png)  

现在我们需要得到这个ipa文件，然后把它下载到我们的系统上（电脑上）。我们可以用sftp。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat11.png)  

一旦我们得到ipa文件，把它后缀名改为zip，然后解压这个文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat12.png)  


在终端（Terminal, 命令行）下，转到其内部的Payload－> Doxygen目录下。如下图。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat13.png)  


你会看到有一个叫做doxMe.sh的shell脚本。如果你看看它的内容，你会看到它把运行Doxygen的工作自动化了。
Doxygen也会运行Graphviz来产生图表，结果会保存在内部一个叫做html的文件夹下。基本上，iNalyzer已经把所有的类信息替我们保存在
内部一个叫做Reversing Files的目录下了，而且它用Doxygen和Graphviz来把信息更友好的展示出来了。这个脚本同时也会把新创建的html文件夹内部的
index.html文件打开。


![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat14.png)  


现在，我们来运行这个脚本，让iNalyzer为我们做所有的事情。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat15.png)  

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat16.png)   


一旦这个命令完成，iNalyer会把新创建的html文件夹内部的index.html文件打开。 下面就是打开的样子。在这里，我用的是chrome。不过，这个工具的开发者
推荐我再进行运行时分析的时候使用firefox浏览器，因为其它浏览器用起来可能会有问题。如下图所示，第一页给出了对整个应用的字符串分析。它把字符串分成了
SQL和URL字符串。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat17.png)    

你也可以看看应用中使用的所有的view controller。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat18.png)    

点击任意的View controller，你可以看到它的方法和属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat19.png)    


你也可以看看Info.plist文件的内容。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat20.png)    


如果你选择Classes这个Tab，在Class Index下面你回看到所有应用中使用的类的列表。有些是苹果自己的类，有些是这个应用的开发者创建的。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat21.png)    


如果你到Class Hierarchy Tab（类层次标签）下，你可以看到以图像方式展示的类信息和它们之间的关系。这会给你大量关于应用如何工作的知识。
这些图使用Graphviz这个工具产生的。

![](http://resources.infosecinstitute.com/wp-content/uploads/090313_1547_IOSApplicat22.png)    


如果你选择文件标签（files tab), 你可以看到iNalyer生成的所有接口文件。


 <br/>
## 总结

 本文我们学习了如何使用iNalyer对iOS应用程序进行静态分析，可以看到它使我们的工作变得非常容易。

本文原文是 [IOS Application Security Part 15 – Static Analysis of IOS Applications using iNalyzer][7]


[1]:https://appsec-labs.com/
[2]:https://appsec-labs.com/iNalyzer
[3]:https://github.com/appsec-labs/iNalyzer
[4]:http://www.graphviz.org/download..php
[5]:http://www.stack.nl/~dimitri/doxygen/download.html
[6]:http://www.graphviz.org/pub/graphviz/stable/macos/
[7]:http://resources.infosecinstitute.com/part-15-static-analysis-of-ios-apps-using-inalyzer/



***
[#5 iOS应用静态分析下的更多文章](http://security.ios-wiki.com/issue-5/)

