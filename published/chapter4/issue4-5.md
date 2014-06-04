###简介

Cycript是一个理解Objective-C语法的javascript解释器，这意味着我们能够在一个命令中用Objective-C或者javascript，甚至2者兼用。它能够挂钩正在运行的进程，能够在运行时修改应用的很多东西。

使用Cycript有如下好处：

- 1.我们能够挂钩正在运行的进程，并且找出正被使用的类信息，例如view controllers，内部和第3方库，甚至程序的delegate的名称。
- 2.对于一个特定的类，例如View Controller, App delegate或者任何其他的类，我们能够得到所有被使用的方法名称。
- 3.我们能够得到所有实例变量的名称和在程序运行的任意时刻实例变量的值。
- 4.我们能够在运行时修改实例变量的值。
- 5.我们能够执行Method Swizzling，例如替换一个特定方法的实现。
- 6.我们可以在运行时调用任意方法，即使这个方法目前并不在应用的实际代码当中。


###安装Cycript

Cycript的官网在[http://www.cycript.org/](http://www.cycript.org/)，最新版本是 0.9.501。
在iOS越狱设备上，默认就有这个工具(参见[这里](http://0oneo.me/blog/2014/03/02/how-to-install-cycript/) 和 [这里](http://www.cycript.org/manual/))，在iOS的命令行输入

    cycript
    
即可。如果你遇到问题，可以在Cydia中把模式切换成开发者，然后搜索Cycript，从Cydia中安装一下。

当然，你直接从网站上下载到Mac上，然后在上传到iOS设备上也行。

###用法举例

下面我们介绍Cycript的用法：
首先用命令

    ps aux

找到要分析的应用的pid，然后用命令：

    cycript -pid xxx
    
如下图所示：

 ![](https://farm3.staticflickr.com/2910/14309553565_bf64140d65_b.jpg)
 
 然后你可以输入ObjectiveC的语法，比如：
 
     [UIApplication sharedApplication]
     
 在Cycrit下：
 
    UIApp  和 [UIApplication sharedApplication] 等效
 
 你甚至可以改提示数字了。此时按Home，让应用切到后台，然后输入如下的命令：
 
    UIApp.applicationIconBadgeNumber=100
    
效果如图：

 ![](https://farm3.staticflickr.com/2932/14122939350_30d84e7b51_z.jpg)
 
    UIApp.applicationIconBadgeNumber=200
 
 效果如图：
 
 ![](https://farm6.staticflickr.com/5561/14307682412_61e8739d7b_z.jpg)
 


###使用Cycript绕过屏幕解锁密码

首先，请确保你的iOS设备设置了锁屏密码，然后请锁屏待机。

然后在你的Mac上ssh进你的iOS设备，

    ssh root@your_ios_device_ip
    
然后执行如下命令：

    cycript -p SpringBoard
    
然后输入：

    SBAwayController.sharedAwayController

如下所示：

    Tedteki-iPad:~ root# cycript -p SpringBoard
    cy# SBAwayController.sharedAwayController
    #"<SBAwayController: 0x1d99aaa0> <SBActivationContext: 0x1d99adf0> activate:  deactivate: "

我们向要打印一个类的所有方法，可以使用[Cycript Tricks](http://iphonedevwiki.net/index.php/Cycript_Tricks)中的printMethods。

继续输入：


    function printMethods(className) {
      var count = new new Type("I");
      var methods = class_copyMethodList(objc_getClass(className), count);
      var methodsArray = [];
      for(var i = 0; i < *count; i++) {
        var method = methods[i];
        methodsArray.push({selector:method_getName(method), implementation:method_getImplementation(method)});
      }
      free(methods);
      free(count);
      return methodsArray;
    }

然后在命令行中执行：

    printMethods(SBAwayController)
    
可以得到如下图所示的信息：

![](https://farm3.staticflickr.com/2916/14336421343_797ecf9c30_b.jpg)

从图中可以发现这个方法：

    {selector:@selector(unlockWithSound:bypassPinLock:),implementation:0x10d6e9}


即方法：
   
    -(void)unlockWithSound:(BOOL)arg1 bypassPinLock:(BOOL)arg2;

如下图：

![](https://farm4.staticflickr.com/3806/14315613644_1657deb4ee_b.jpg)

在cycript中的输入：

   
    [SBAwayController.sharedAwayController unlockWithSound:1 bypassPinLock:1]
    
 你的iOS设备就实现了无密码解锁。


###小结
Cycript功能非常强大，能够在运行时对iOS应用做修改，我们会在后面的章节详细介绍更多用法。
   

***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)
