####class-dump-z简介

class dump是一个命令行工具，它可以dummp出破解之后的iOS二进制文件的头文件信息（后面我们会介绍破解iOS应用的工具Clutch, 这个工具需要安装在iOS设备上，所以放在下一章介绍）。

这些信息与 otool -ov 命令提供的信息是一样的，但它更紧凑，更易读。

####为什么要使用class-dump

你可以看到闭源应用程序，框架(framework)和软件包(bundle)的头文件，了解它内部是如何设计的。

####下载与安装

可以从这里[http://networkpx.googlecode.com/files/class-dump-z_0.2a.tar.gz](http://networkpx.googlecode.com/files/class-dump-z_0.2a.tar.gz)下载。

执行如下命令解压文件并把可执行程序拷贝到/usr/bin目录：
> * **tar -zxvf class-dump-z_0.2a.tar.gz**
> * **sudo cp mac_x86/class-dump-z /usr/bin/**

如下图所示：  

![](https://farm3.staticflickr.com/2940/14096714130_da7874626e_c.jpg)

现在，在命令行输入class-dump-z，你可以得到如下的提示信息：


    ZPs-MBP:class-dump-z admin$ class-dump-z
    
    Usage: class-dump-z [<options>] <filename>
    
    where options are:
    
      Analysis:
        -p         Convert undeclared getters and setters into properties (propertize).
        -h proto   Hide methods which already appears in an adopted protocol.
        -h super   Hide inherited methods.
        -y <root>  Choose the sysroot. Default to the path of latest iPhoneOS SDK, or /.
        -u <arch>  Choose a specific architecture in a fat binary (e.g. armv6, armv7, etc.)
    
      Formatting:
        -a         Print ivar offsets
        -A         Print implementation VM addresses.
        -k         Show additional comments.
        -k -k      Show even more comments.
        -R         Show pointer declarations as int *a instead of int* a.
        -N         Keep the raw struct names (e.g. do no replace __CFArray* with CFArrayRef).
        -b         Put a space after the +/- sign (i.e. + (void)... instead of +(void)...).
        -i <file>  Read and update signature hints file.
    
      Filtering:
        -C <regex> Only display types with (original) name matching the RegExp (in PCRE syntax).
        -f <regex> Only display methods with (original) name matching the RegExp.
        -g         Display exported classes only.
        -X <list>  Ignore all types (except categories) with a prefix in the comma-separated list.
        -h cats    Hide categories.
        -h dogs    Hide protocols.
    
      Sorting:
        -S         Sort types in alphabetical order.
        -s         Sort methods in alphabetical order.
        -z         Sort methods alphabetically but put class methods and -init... first.
    
      Output:
        -H         Separate into header files
        -o <dir>   Put header files into this directory instead of current directory.
        
        
       
上面的-H 命令可以把头文件信息放到各自的文件中，-o 后面可以指定一个存放头文件的目录。

我们用这两个命令来到处应用的头文件信息。

> * class-dump-z -H AppName/Payload/AppName.app/AppName   -o storeheaders


再一次提醒一下，这里的应用是用Clutch破解之后的，如果直接用class-dump-z dump从App Store下载的二进制文件，只能得到加密后的信息。

下一章 **iOS设备上的工具** 会详细介绍Clutch的用法


####获得iOS应用程序的类信息

之前我翻译过一篇文章[iOS应用程序安全(2)-获得iOS应用程序的类信息](http://wufawei.com/2013/11/ios-application-security-2/)，里面分别以Apple自带的地图和从App Store下载的Yahoo天气为例，介绍了class-dump-z获得iOS应用程序的详细过程。

####小结

本文简要介绍了class-dump-z的作用，下载、安装和具体用法。

****












***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)