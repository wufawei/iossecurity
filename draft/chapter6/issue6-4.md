##引言

本文我们将在一个例子程序上看如何用Cycript做method Swizzling。

第一件事情就是下载Xcode例子工程。你可以从[这][1]下载。或者你也可以从[这][2]下载二进制文件。推荐你下载Xcode例子工程，看看源代码。

请确保使用你自己的证书来签名。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat1.png)

一旦你让这个app在设备上运行起来，ssh进设备，然后用Cycript挂钩这个进程。
你可以通过命令cycript -p [app_id] 来挂钩进入任意进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat2.png)

如你所见，这个app有一个登录框。请注意在本文中，我们只会在点击 Login Method 1这个按钮的时候绕过登录。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat3.png)

用户名和密码是 admin:password。登录后可以进入管理页面。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat4.png)


如果输入的用户名和密码有误，那会得到一个错误提示。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat5.png)

我们的目标就是绕过这个登录view。

首先，我们先找到这个应用对用的root view controller.在cycript中用下面的命令
UIApp.keyWindow.rootViewController

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat6.png)

既然我们在应用中看到的第一个视图就是这个登录页面，我们可以确定负责显示这个视图的view controller是我们用前一个命令找到的navigation controller的一部分。我们可以用navigation controller的visibleViewController属性来找到当前的视图。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat7.png)


完美。现在让我们写个函数打印出该view controller的所有方法吧。这个方法是从Cycript技巧页面拿过来用的。我建议你仔细看看这个页面，能发现很多不错的代码。

下面就是我用的代码。

	1. function printMethods(className) {
	2. var count = new new Type("I");
	3. var methods = class_copyMethodList(objc_getClass(className), count);
	4. var methodsArray = [];
	5. for(var i = 0; i < *count; i++) {
	6. var method = methods[i];
	7. methodsArray.push({selector:method_getName(method), implementation:method_getImplementation(method)});
	8. }
	9. free(methods);
	10. free(count);
	11. return methodsArray;
	12. }


![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat8.png)

现在让我们打印出当前view controller的所有方法。请注意这个函数取的是类名，在这里是ViewController

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat9.png)

获取方法名称的另一个方法就是使用isa.messages属性。根据苹果的[官方文档][3]，isa是一个指向类结构的指针。

下面是从同一页摘取的文字。

> “当一个新对象创建的时候，其内存会被分配，实例变量会被初始化。在对象的变量里面第一个就是一个指向它的类对象的指针。这个指针，叫做isa, 使得
> 这个对象能够访问它的类，通过这个类，能够找到所以它继承自的类。”

下面这个来自[苹果文档][3]的图解释得很清晰。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat10.png)

所以，什么是messages属性呢？首先我们必须要知道什么是分发表（dispatch table）。分发表包含了很多条目，这些条目关联方法的selector和方法在类的地址。
让我们看一下从苹果官方的截图。


![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat11.png)



> “给一个对象发送消息的时候，消息函数会根据对象的isa指针到类结构中的分发表查找对方的方法选择器（method selector）。如果没有找到，那objc_msgSend用这个指针找到其superclass，然后查找superclass
> 的分发表。连续的查找失败会让objc_msgSend一直依次查找类层次直到NSObject。一旦它定位到选择器（selector），objc_msgSend就调用表中的这个方法并且把它接收的对象数据结构传递给它。
> 这就是在运行时选择方法实现的方法。或者用面向对象编程的术语，这些方法和消息是动态绑定的。”
> 
> 
> 现在，很容易就能猜到messages属性是能发给类实例或者类本身的方法的消息列表。这将是一个非常大的列表，因为isa指针会选择从它父类一直到NSObject的消息。上面有一行值得特别注意。
> “这就是在运行时选择方法实现的方法。或者用面向对象编程的术语，这些方法和消息是动态绑定的。”




因为方法和消息是在运行时绑定的，所以我们能够更改某个特定消息的方法实现。

在这里，让我们打印出App Delegate类的所有消息。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat12.png)


直接使用messages属性也一样可以。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat13.png)

在这里，我们关心的时候用来显示登录页的view controller，我们之前就发现它的名称为ViewController.所以打印出这个VC的所有消息。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat14.png)

在输出的顶部，你可以看到这个VC的一些方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat15.png)

方法validateLogin看起来很有趣。让我们看看class-dump-z输出的关于这个方法的信息。如果你对class-dump-z不熟悉，请参看本系列的[这篇文章](http://security.ios-wiki.com/issue-3-5/)。

下面就是我们从class-dump-z的输出中找到的关于ViewController的相关信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat16.png)


正如我们看到的，方法validateLogin返回一个BOOL类型值。从这我们可以推断这个方法验证用户的用户名和密码是否正确，正确返回YES，否则返回NO。
有Cycript在手，我们可以改变某个特定消息的实现。让我们来实现一个方法让它总是返回TRUE。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat17.png)


因此，R.H.S （Right Hand Side）是一个javascript函数，总是返回true。让我们现在点击应用的登录页面的Login Method 1。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat18.png)

可以看到，认证成功，应用让我们进入管理页面了。我们使用Cycript执行了method swizzling，绕过了登录框。


##一些其他有趣的事情。

现在我们看到method swizzling是如何工作的，去了解下绕过这个验证的其他方法也会非常有趣。从class-dump-z的输出结果，我们可以看到一旦validateLogin返回TRUE。方法pushLoginPage就会被调用。其他一些页面可能叫做pushUserPage, 或者pushLoginSuccessfulPage等等。我们不必需要验证一定要是TRUE。我们可以自己调用这个方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1533_IOSApplicat19.png)

因为这是个实例方法，我们通过UIApp.keyWindow.rootViewController.visibleViewController 得到实例。请注意这样做可能导致crash，因为后续被push进的view controller
可能对输入的用户名和密码有依赖。如果你想挑战一下，不妨试试绕过Login Method 2。


##总结

本文我们学习了如何利用Cycript来进行method swizzling。


本文原文是 [iOS应用程序安全(8)-用Cycript进行Method Swizzling](http://wufawei.com/2013/11/ios-application-security-8/)

[1]:https://dl.dropboxusercontent.com/u/34557464/MethodSwizzlingDemo.zip
[2]:https://dl.dropboxusercontent.com/u/34557464/MethodSwizzlingDemo.ipa
[3]:http://developer.apple.com/library/ios/
[4]:http://wufawei.com/2013/11/ios-application-security-2/
[5]:http://resources.infosecinstitute.com/ios-application-security-part-8-method-swizzling-using-cycript/


***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)
