本文将使用的[GDB-Demo][1]例子程序可以从我的github账户上下载。请确保在你的设备上安装和运行。

现在让我们SSH进入设备。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat1.png)

现在我们开启GDB，然后让GDB在应用开启之后就挂钩这个应用。可以通过命令 attach -waitfor Appname来完成。你也可以
在设备上运行这个应用，然后用attach命令挂钩这个运行的进程，如下图所示。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat2.png)

一旦GDB挂钩进了这个应用，你会注意到这个应用目前是在暂停状态。你可以用 c 命令让这个应用继续执行。不过在继续执行之前，
让我们先做些调查。和任何其它架构一样，ARM中的内存也被分为寄存器（register）。所有的寄存器都是32位的（iOS 7中是64位的），并且它们的目的
就是保存和相互之间移动数据。你可以使用 info registers命令来查看关于这些寄存器的信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat3.png)

请注意这个命令并没有把ARM中的所有寄存器都打印出来。要打印所有的寄存器，使用 info all-registers命令。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat4.png)

要导出汇编信息，使用disassemble 或者 disas 命令。这会给出后续几条指令的一些汇编信息。我们通过在
disas命令后面提供函数名称来导出某个特定函数的汇编。例如要导出main函数的汇编，使用命令 disas main。如下图。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat5.png)
 
我们看看刚刚在设备上安装的应用，可以看到只是一个要求（输入）用户名和密码的简单应用。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat6.png)

我们也可以从用class-dump-z对这个应用导出的信息中找到有个类叫ViewController和一个方法叫做-(void)loginButtonTapped:(id)tapped;

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat7.png)


使用GDB，我们可以在应用中设置断点。只需要输入要断下来的方法名称。使用命令 b functionName。你也可以提供不带类信息的方法签名，
如果你不确定的话，GDB会咨询你想要在那个类上设置断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat8.png)


请注意，实例方法前缀都带有一个"-"，而类方法前缀带有"+"，如下图所示。例如，sharedInstance是一个类方法，方法一个单例类的共享实例。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat9.png)

可以通过命令info breakpoints看到所有的断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat10.png)

通过命令 delete 和 断点的ID就可以删除任何断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat11.png)

不管怎样，先给方法loginButtonTapped: 设置一个断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat12.png)


现在我们可以用命令continue或者c让应用重新run起来。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat13.png)

现在点击应用的登录按钮。这样就会触发我们的断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat14.png)

我们可以用disassemble命令查看随后的一些汇编信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat15.png)

要在任意的指令前面下断点，请在那个指令的地址前面加上"*"


![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat16.png)

Objective-C是基于消息的，任何时候一有消息被发送，objc_msgSend
就会被调用。

在我们打印出的loginButtonTapped: 的汇编代码当中，这里有许多的objc_msgSend调用。要找出这个调用的一个好方法就是查找blx指令。
在你看到blx指令的地方，你可以确认有一个objc_msgSend正在被调用。


![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat17.png)

当有新方法被调用，或者有属性（property）被访问的时候，objc_msgSend就会被调用。所以，如果我们在objc_msgSend下一个断点，我们可以
打印出正被调用的方法和调用这个方法的对象，这将帮助我们理解app的整个流程。我们已经在本系列的[这篇](http://security.ios-wiki.com/issue-4-6/)文章中学习过Snoop-it能够找到所有被追踪的调用。
 要找出正在被调用的方法，我们首先需要查看ARM的调用约定（call convention）。下面是从[Wikipedia][5]截取的关于ARM调用约定的图。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat18.png)

其中有一行很重要。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat19.png)

因此，我们可以给每一个objc_msgSend设置断点，然后使用r0-r3寄存器的值找到传递给这个函数的参数。
我们先看看objc_msgSend的签名。下面是Apple [官方文档][6]的截图。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat20.png)

因此这个函数的前2个参数是self 和 op，self是一个用来接收这个消息的某个类的实例，op是要处理这个消息的方法的选择器(selector)。
选择器（selector）是关于这个消息的签名。例如，如果一个方法的原型为 -(void)addOjectsToArray:(NSArray *)array，那么它的签名就是
addOjectsToArray:。我们也知道r0-r3用来保存传递给子程序的参数值，因此我们可以推断r0会包含self，而r1会包含op。

我们通过例子来理解。先给objc_msgSend下一个断点，然后继续执行知道断点被触发。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat21.png)


我们已经知道，r0会包含一个用来接收这个消息的某个类的实例，r1会包内选择器，从r2开始会是传递给方法的参数。不过，我们要先
学下命令 x。x代表检查（examine），会以多种格式帮助我们查看内存。我们能够制定我们想要查看的内存的格式。要找出这个命令的所有
选项，使用命令 help x。




![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat22.png)

我们先检查r0。我们知道r0会包含一个用来接收这个消息的某个类的实例，因此我们要使用的格式是x/a。我们在r0签名使用了$，因为我们想要查看内存，因此
使用$。


![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat23.png)

我们可以看到接收者是UIRoundedRectButton类的一个实例。现在我们再检查下r1寄存器的值。我们知道它包含一个选择器，例如，方法的签名。
这是一个字符串，因此我们使用 x/s。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat24.png)


现在，我们需要找出传递给这个方法的参数。这个可能会有些棘手，因为我们并不知道r2的格式。但是注意到选择器是respondsToSelector:
用常识我们可以推断参数可能是一个选择器，因此我们再次使用x/s来检查内存。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat25.png)

所有参数就是debugDescription。从方法的选择器我们可以看到，这个函数只有一个参数，因此我们不必进一步检查其他寄存器。所以，
现在我们可以说正在被调用的方式像下面这样。

-[UIRoundedRectButton respondsToSelector:@selector(debugDescription)];


这里会有太多的objc_msgSend会被调用，一个一个简单会非常痛苦。因此，让我们把这个过程自动化。在本系列的[第3篇][3]文章中，我们学到了如何
用gdb在断点触发的时候打印信息。我们这样也用用。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat26.png)


现在输入命令c继续，你可以看到所有被调用的方法。这可以告诉我们很多这个应用的内部信息。


![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat27.png)


让我们试试以Objective-C类似的语法打印出这些东西。我们将要使用苹果[文档][6]中的class_getName。如你所见，它需要提供类对象作为参数，
因此我们传递r0给它。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat28.png)

现在像下面这样重写调用命令。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat29.png)

输入命令c继续，现在你可以看到，信息是更可读的方式了。

![](http://resources.infosecinstitute.com/wp-content/uploads/110613_1702_IOSApplicat30.png)


这会告诉我们很多关于应用内部发生什么的信息。在接下来的文章中，我们将使用本文学到的东西来学习如何使用GDB执行运行时操作。


本文原文 [iOS应用程序安全(21)-ARM和GDB基础](http://wufawei.com/2013/11/ios-application-security-21/)


[1]:https://github.com/prateek147/gdb-demo
[5]:http://en.wikipedia.org/wiki/Calling_convention
[6]:https://developer.apple.com/library/mac/documentation/cocoa/reference/objcruntimeref/Reference/reference.html
[7]:http://resources.infosecinstitute.com/ios-application-security-part-21-arm-gdb-basics/







***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)

