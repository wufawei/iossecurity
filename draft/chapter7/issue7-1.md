
###Cydia Substrate

Cydia Substrate(以前叫做MobileSubstrate)是一个框架，允许第三方的开发者在系统的方法里打一些运行时补丁，扩展一些方法，类似OS X上的 Application Enhancer。

saurik为Substrate写了非常全的[介绍文档](http://www.cydiasubstrate.com/id/264d6581-a762-4343-9605-729ef12ff0af/)。

Cydia Substrate有3部分组成：

> * MobileHooker
> * MobileLoader
> * safe mode


###MobileHooker

MobileHooker用来替换系统函数，这个过程也叫Hooking。有如下的API可以使用：

    IMP MSHookMessage(Class class, SEL selector, IMP replacement, const char* prefix); // prefix should be NULL.
    
    void MSHookMessageEx(Class class, SEL selector, IMP replacement, IMP *result);
    
    void MSHookFunction(void* function, void* replacement, void** p_original);


MSHookMessageEx用来替换Objective-C的函数，MSHookFunction用来替换C/C++函数。具体用法参见[这里](http://iphonedevwiki.net/index.php/MobileSubstrate) 和 [这里](http://www.cydiasubstrate.com/api/c/MSHookFunction/)

###MobileLoader

MobileLoader把第3方补丁程序加载进入运行的程序中。
MobileLoader首先会通过DYLD_INSERT_LIBRARIES把自己加载进入目标程序，然后它会在/Library/MobileSubstrate/DynamicLibraries/中找到需要加载的动态链接库并加载它们，控制是否加载到目标程序，是通过一个plist文件来控制的。如果需要被加载的动态库的名称叫做foo.dylib，那么这个plist文件就叫做foo.plist，这个里面有一个字段叫做filter，里面写明需要hook进的目标程序的bundle id。

比如，如果只想要foo.dylib加载进入SpringBoard，那么对应的plist文件中的filter就应该这样写：

    Filter = {
      Bundles = (com.apple.springboard);
    };
    
    
关于使用DYLD_INSERT_LIBRARIES来注入代码的例子可以参见[这里](http://blog.timac.org/?p=761)


###Safe mode

当编写的扩展导致SpringBoard crash的时候，MobileLoader会捕获这个异常，然后让设备进入安全模式。在安全模式中，所有的第3方扩展都会被禁用。

下面这些signal会触发安全模式：
SIGTRAP
SIGABRT
SIGILL
SIGBUS
SIGSEGV
SIGSYS


###小结

本文简要介绍了Cydia Substarte。后面要介绍的越狱程序Tweak，就是利用Cydia Substrate中的MobileLoader来加载的。



***
[# iOS越狱程序编写下的更多文章](http://security.ios-wiki.com/issue-7/)

