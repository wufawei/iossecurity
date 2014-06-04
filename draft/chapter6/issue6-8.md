[在这篇文章](http://security.ios-wiki.com/issue-5-4/)我们看到了如何使用iNalyzer对iOS应用进行静态分析。本文我们将看看如何用iNalyer对iOS应用进行运行时分析。我们能够在运行时调用方法，能够在应用的某个特殊时间找出特定实例变量的值，基本上能做我们用Cycript做的所有事情。


在[这篇文章](http://security.ios-wiki.com/issue-5-4/)当中，我们成功的用Doxygen生成了html文件，并且打开它看到了关于这个应用的类信息和其他信息。我们将使用Firefox浏览器进行运行时分析。这个工具的开发者推荐我再进行运行时分析的时候使用Firefox浏览器，因为其它浏览器用起来可能会有问题。 不过，对我来说，在chrome上好像也工作正常。


要打开运行时解释器，首先需要打开Doxygen为你想要分析的应用生成的index.html文件，然后双击左剪头键。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat1.png)

如上图所示，你可以看到有一个可以输入命令的控制台在顶部出现。第一件事情就是告诉iNalyer你设备的IP地址，在这里是10.0.1.23。输入IP地址后然后确定（按Enter）。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat2.png) 

一旦IP地址设置好之后，请确保我们要分析的应用在设备上是打开的（例如，在前台），并且你的设备没有休眠。这非常重要，
因为如果你的应用在后台或者你的设备在休眠，那你的应用是会被操作系统给暂停的，因此就不可能对这个应用进行任何运行时分析。

一旦应用打开，在控制台输入任意命令，如你使用Cycript会输入的一样。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat3.png)  


正如我们看到的那样，我们会得到一个响应。我们现在可以输入我们想要输入的任何cycript命令。

让我们隐藏应用的状态栏。我们可以用这个命令， [[UIApplication sharedApplication] setStatusBarHidden:YES animated:YES];


![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat4.png)  

可以看到，我们并没有得到任何响应，那是因为这个方法返回空（void）。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat5.png)  


不过，应用的状态栏已经隐藏起来了。我们在最上面已经看不到时间了。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat6.png)  

类似的，我们可以找到这个应用的delegate类。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat7.png)  

我们也可以设置应用的提醒数字。这里我们设置为9000。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat8.png)  


可以看到，提醒数字成功设置。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat9.png)  

因为这和有一个cycript控制台类似，我们也可以输入javascript代码和任何其他Cycript文档中的命令。
下面就是我输入的从[Cycript tricks][1]页面引用的一个命令。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat10.png)  

类似的，我可以同时用javascript和Objective-C的语法来创建函数。如果你对这里说的Cycript不太理解，请
参考本系列前面介绍Cycript和它的详细用法的文章。 

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat11.png)

我可以在想要使用这个函数的任何时刻用它。

![](http://resources.infosecinstitute.com/wp-content/uploads/090913_1208_IOSApplicat12.png)


在本系列的[第9部分][2]，我们介绍了Snoop-it。iNalyer和Snoop-it非常类似。不过二者都有优点和不足。
在本文写关于Snoop-it的时候，它并不支持method swizzling而iNalyer支持。蕾丝的，iNalyer不允许我们
监控API调用而Snoop-it可以。因此，这两个应用都有它们的优点和不足。

<br>
## 总结

本文我们学习了如何利用iNalyer来对iOS应用进行运行时分析。对于任何对iOS应用程序安全感兴趣的人来说，iNalyer都是武器库中非常棒的工具，它使得我们的工作更容易、更有效率。

References

iNalyzer
https://appsec-labs.com/iNalyzer



本文原文是 [iOS应用程序安全(16)-使用iNalyzer对iOS应用进行动态分析](http://wufawei.com/2013/11/ios-application-security-16/)

[1]:http://iphonedevwiki.net/index.php/Cycript_Tricks
[2]:http://wufawei.com/2013/11/ios-application-security-9/ 
[3]:http://resources.infosecinstitute.com/ios-app-security-part-16-runtime-analysis-of-ios-apps-using-inalyzer/




***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)

