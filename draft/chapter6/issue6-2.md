
##用Cycript进行实时修改

本文，我们将使用Yahoo Weather应用来执行所有的测试。它有一个清爽和优雅的UI来提供不同地区的天气信息。

一旦Yahoo Weather应用被安装好，请确保它运行在前台。这是因为如果应用在后台，那它就会被暂停，你也不能对它做啥。一旦应用跑起来，你可以先找到其进程id，然后用cycript -p挂钩其进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat5.jpg)


如果挂钩成功，你可以得到一个Cycript解释器。你可以通过Objective-C的语法
[UIApplication sharedApplication].来得到实例。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat6.jpg)



如下图所示，你也可以通过Cycript解释器定义变量。在这里，我定义了一个变量a来代表
[UIApplication sharedApplication]。 请注意，命令的L.H.S(Right Hande Side)是Javascript，而R.H.S(Right Hand Side)是Objective-C语法。这就是Cycript美的地方。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat7.jpg)


Cycript默认就有这个变量，用它可以很容易的得到应用的实例。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat8.jpg)

为了找到这个应用的delegate，我们可以用 [UIApplication sharedApplication].delegate.
不过既然我们已经定义了一个a代表应用的实例，我们可以用下图的方式来得到delegate。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat9.jpg)


因此，我们现在知道这个delegate类的名称是YWAppDelegate。因此delegate文件就是YWAppDelegate.h和YWAppDelegate.m。让我们在它运行的时候试着让它调用几个方法。看这个应用给出如下的图片。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat10.jpg)


正如你所看到的那样，这个应用的状态栏被隐藏了。我们可以调用方法让状态栏显示。同时，请确保我们在进行运行时分析这个应用的时候，这个应用运行在前台。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat11.jpg)


这是这个应用现在的样子。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat12.jpg)


正如你看到的，现在状态栏是可见的了。让我们看看我们能否修改这个应用的提醒数字。提醒数字是在应用程序右上角显示的数字。通常它是指一个应用收到的push通知数目。在邮件应用中，它也可以指未读邮件的数量。在Yahoo Weather这个应用中，这里没有push通知的概念，因此，这里没有在应用程序图标的右上角显示数字。这个提醒数字在本地可以很方便的设置。让我们试试给它设置提醒数字为999。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat13.jpg)

现在我们按Home键，可以在应用的桌面图标上看到提醒数字。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat14.jpg)

完美！

现在我们看看还能找出些什么有趣的东西。为了找出当前的view controller，我们首先需要找出keyWindow。 keyWindow是一个用来接受用户交互（例如点击事件）的window。如果你想要找出应用所有的window，可以执行下面的命令。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat15.jpg)


为了找出在特定时刻的keyWindow。可以这么做。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat16.jpg)


root view controller可以用如下的方法找出来。

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat17.jpg)


如你所见，rootViewController的名字是YahooSlidingController。从它的名字可以推断，它是如下图的slider所使用的类

![](http://resources.infosecinstitute.com/wp-content/uploads/061013_1436_iOSApplicat18.jpg)




因此，这个类基本上是其他所有的view controller的一个外观（facade）类。这意味着当某个菜单被选中的时候，YahooSlidingController负责调用对应的view controller来显示。


<br>
##总结

本文我们介绍了如何在越狱设备上安装Cycript，如何挂钩运行的进程和找出应用的属性信息。我们也展示了如何在应用程序的沙盒内（由我们自己）调用我们自己的函数。在下一篇文章，我们将找出特定类的所有方法并修改其实现。我们也会看看如何去修改特定类的实例变量。


###References: 

 Cycript

http://www.cycript.org/ 

 Cycript tricks

http://iphonedevwiki.net/index.php/Cycript_Tricks 

本文原文是 [iOS应用程序安全(4)-用Cycript进行运行时分析(Yahoo天气应用)](http://wufawei.com/2013/11/ios-application-security-4/)


[1]:http://www.cycript.org/
[2]:http://resources.infosecinstitute.com/ios-application-security-part-4-runtime-analysis-using-cycript-yahoo-weather-app/


***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)

