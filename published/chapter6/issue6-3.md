##引言


在前一篇文章中，我们学习了如何在设备上安装使用Cycript。本文我们将使用一些运行时分析的高级技巧。我们将看看如何获得特定类（方法，实例变量）的值并且在运行时修改它们。


##找出特定类的方法

现在假定我们正在一个程序运行的时候分析它的流程，那么，知道特定类或者特定view controller正在调用哪些方法将会给我们极大的帮助。因为Cycript能够混合使用Objective-C和javascript，我们能够写一个即有Objective-C又有Javascript语法的函数。我们那个能够在解释器中定义函数，然后在任意时候使用它们以帮助我们找到有用信息。一个能够找到这些有用代码块的地方位于[这里][1]。本文我们将使用这里的代码块。

首先，确保我们挂钩进了正在运行的进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat1.png)


我们先定义一个能够打印出一个指定类的方法的函数。你可以在[这][1]找到这些Cycript tricks。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat2.png)


既然我们已经定义好了方法，那么我们可以输入任何类，然后得到对应的方法列表。从上一片文章中，我们知道Yahoo Weather的delegate是YWAppDelegate. 因此，这里试试这个类包含的所有方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat3.png)


上面打印出了YWAppDelegate定义的方法。 在@selector后面的就是方法名称。请注意，这里会给出关于私有方法的名称，也会给出定义在类中的属性的setter和getter函数。

类似的，我们可以打印出YahooSlidingViewController的方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat4.png)


我们知道YahooSlidingViewController管理着slide菜单，并且充当着外观者（facade）的作用。为了找出真正负责显示天气的view controller，我们可以使用下面的命令。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat5.png)


因此，YWMainViewController就是当前负责显示天气的view controller。因此，下面的view实际上来自YWMainViewController。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat6.png)


让我们打印出YWMainViewController的方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat7.png)


你可以看到，这里有个方法叫做userDidrequestUpdate.

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat8.png)


从方法名称可以看出，一旦用户下拉刷新，这个方法就会被调用。有Cycript，我们可以随时调用这个方法。我们要先引用这个view controller，然后在其上调用这个方法。如下图所示。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat9.png)


可以看到，即使我们并没有下拉刷新，应用也完成了更新。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat10.png)


如上面所说，这些方法也包含属性的setter和getter。

从安全的角度来说，能够在运行时操纵应用程序给了我们巨大的优势。我们能够在我们想要的时候调用任何方法。想像一下当用户第一次登陆的时候输入其用户名和密码，一旦登陆成功，一个叫做 didLogin的方法会被调用。我们可以直接调用这个方法而不用输入任何用户名/密码的组合。

如果我们能够把特定view controller的变量都打印出来的话，那会很有用处的。因此，我们定义一个能够打印出所有实例变量的函数，你可以从[这][1]找到代码块。

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat11.png)

现在，我们打印出YWMainViewController的实例变量。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/061713_1158_IOSApplicat12.png)

你可以看到，这里有一个location view controller的实例变量，你可以向左和向右swipe滑动来查看不同的地方。从名字来看，locationViewControllers
更像是一个viewcontroller数组。使用Cycript，我们能够打印出该变量的值。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/061713_1158_IOSApplicat13.png)


现在我们向右滑到New York

![](http://resources.infosecinstitute.com/wp-content/uploads/061713_1158_IOSApplicat14.png)

现在我们打印出这个变量

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/061713_1158_IOSApplicat15.png)

可以看到，这个数组始终包含3个location view，其他的都是nil。它并不把所有的location view controller都包含进来，以便更好的管理内存。所以在任何时刻，我们能够拥有当前正在看的，左边和右边的view controller。当我们想要去另一个不同地方的时候，它会自动的使得当前可见的位于中间并且实例化其左边和右边的view controller，因此当我们滑动的时候，不会感觉到任何的时延。这是个编写不占用过多内存的代码的好方法。



##总结

在前两篇文章中，我们对Yahoo weather应用进行了运行时分析。在接下来的文章中，我们将看到更多Cycript的技术，并且我们将关注method swizzling。


###References:

Cycript

http://www.cycript.org/

Cycript tricks

http://iphonedevwiki.net/index.php/Cycript_Tricks



本文原文是 [iOS应用程序安全(5)-用Cycript做运行时分析的高级技巧(Yahoo天气应用)](http://wufawei.com/2013/11/ios-application-security-5/)


[1]:http://iphonedevwiki.net/index.php/Cycript_Tricks
[2]:http://resources.infosecinstitute.com/ios-application-security-part-5-advanced-runtime-analysis-and-manipulation-using-cycript-yahoo-weather-app/



***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)


