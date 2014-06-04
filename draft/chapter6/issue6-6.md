
本文我们将看看如何使用GDB来对iOS应用进行运行时分析。 在前面的文章中，我们已经查看了如何使用Cycript来分析和操作iOS应用的运行时行为。我们学习了如何执行method swizzling，并且调用我们自己的方法而不是原来的实现。因此，为什么我们还需要GDB呢？Cycript并不运行我们设置断点，不允许在某个特定指令后修改变量和寄存器的值。用GDB，我们可以更深入应用，观察底层的汇编指令，操作寄存器的值，因此可以完全改变程序的运行流程。


你可以从github下载[GDB-Demo][3]。然后确保安装和运行它到你的设备上。这个例子应用是一个简单的单视图应用，要求你输入用户名和
密码的组合来登入。然后它在本地验证你输入的凭证，如果用户名/密码输入正确，它会让你登录进去。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat2.png)

一旦应用安装到了设备上，ssh进入设备。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat2.png)
然后在你的设备上开启GDB-Demo这个应用。在GDB中，使用命令 attach GDB-Demo.PiD 附加到这个运行进程，这里的PID是GDB-Demo应用的进程ID。你那里的PID可能不一样。输入attach GDB-Demo然后点击TAB。就会给出你要追加的正确的进程ID。一旦你按了enter，GDB会挂钩进这个运行的进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat3.png)


从上一篇文章我们已经知道这个应用的类信息。我们知道它有一个方法叫做loginButtonTapped:。因此我们给它设置一个断点，然后输入命令c来继续运行这个应用。
![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat4.png)

现在输入任意的用户名和密码组合，然后点击登录。断点就会被触发。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat5.png)

使用disas来打印出这个函数的汇编信息。现在我们知道验证就会发生在这个函数内部，因为我们从这个应用的类信息里找不到其他感兴趣的其他相关信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat6.png)

每当一个外部方法或者属性被访问的时候，objc_msgSend就会被调用。不过，在任何程序中msgSend都会被调用成千上万次。
我们只关心和这个函数（loginButtonTapped:）相关的objc_msgSend调用。因此，我们可以找出所有调用objc_msgSend的指令的地址，然后给它设置断点。
一个非常简单的方法就是寻找blx指令，注意它(blx)的地址，然后为它设置一个断点。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat7.png)


现在我已经为这个函数调用objc_msgSend的入口设置了断点。现在我们一个一个的来看objc_msgSend指令，打印出寄存器的值，看看是否有感兴趣的。
我们将要打印出每个objc_msgSend调用的r1的值。如果没有什么感兴趣的，输入命令c继续直到下一个断点触发。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat8.png)

这里有些有意思的东西。如果我们看看上图的底部，我们会看到方法isEqualToString:被调用了。因此这是一个和特定字符串的比较。利用从上一篇文章获得的知识
我们可以知道寄存器r2会包含传递给这个函数的参数。并且，如果你有编写Objective-C代码的经验，你会知道每个Objective-C的对象都是一个指针。isEqualToString:这个函数也同样接收一个字符串指针作为参数，保存在r2寄存器中。要找出这个对象的值，GDB有一个特定的命令po，能够打印出这个寄存器中的指针的值。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat9.png)

因此，这个正被比较的字符串是“Admin”。这看起来像是用户名。看起来工作已经完成了一半。你也可以用如下图的方式打印出r2的值。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat10.png)

现在更明智的做法就是重新在应用中把用户名输入为Admin。这是因为执行流程可能还走不到检查密码的地方（因为用户名不对）。因此，让我们输入Admin作为
用户名，输入任意的东西作为密码。让我们再次设置断点，然后看看我们是否能够找出密码。在经过一段做同样事情的时间之后，断点将会在isEqualToString:被调用的时候被触发。打印出r2的值，可以看到，密码是HELLOIOSAPPLICATIONEXPERTS。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat11.png)

现在我们输入找到的用户名和密码的组合，我们将看到。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat12.png)

另一个做到同样事情的方法就是操作寄存器的值。在汇编代码中，我们可以看到有2个调用cmp指令的地方。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat13.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat14.png)

在这两个地方，r0寄存器的值都与0做比较，然后根据比较结果做决定。让我们为这两个地方都设置一个断点然后继续运行应用。

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat15.png)

一旦断点触发，设置r0寄存器的值为1.你可以通过命令 set $r0 =1做到。在另一个地方做同样的事情然后继续运行应用。
你会看到你成功登陆，即使你没有输入任何用户名/密码组合。


![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat16.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/111113_1041_IOSApplicat17.png)


顺便说一下，下面是我们破解的loginButtonTapped:的代码。


	1. - (IBAction)loginButtonTapped:(id)sender {
	2. if([_usernameTextField.text isEqualToString:@"Admin"] && [_passwordTextField.text isEqualToString:@"HELLOIOSAPPLICATIONEXPERTS"]){
	3. [self performSegueWithIdentifier:@"adminPage" sender:self];
	4. }else{
	5. [[[UIAlertView alloc] initWithTitle:@"Error" message:@"Incorrect Username or password" delegate:nil cancelButtonTitle:@"Ok" otherButtonTitles:nil] show];
	6. }
	7. }
	
	

本文我们查看了如何通过GDB在运行时操作应用的执行流程。在整个逻辑都在一个函数内部的情况下，关于GDB的知识特别有用，因为我们不能够使用Cycript的method swizzling技术。掌握好GDB和ARM汇编的知识，修改和操作应用的执行流程的能力只受你的想象力限制。


本文原文 [iOS应用程序安全(22)-使用GDB进行运行时分析和操作](http://wufawei.com/2013/11/ios-application-security-22/)


[1]:http://wufawei.com/
[2]:http://wufawei.com/2013/11/ios-application-security-8/
[3]:https://github.com/prateek147/gdb-demo
[4]:http://wufawei.com/2013/11/ios-application-security-7/
[5]:http://wufawei.com/2013/11/ios-application-security-21/
[6]:http://resources.infosecinstitute.com/ios-application-security-part-22-runtime-analysis-manipulation-using-gdb/





***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)

