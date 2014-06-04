上一节我们介绍了IDA，这里我们介绍另一款反汇编工具：Hopper，它有OS X和Linux版本，能够反汇编32/64位Mac，Linux，Windows和iOS可执行文件。


Hopper Disassembler v3 - Personal License版本在中国售价 ￥580.01相对于IDA来说相当便宜。

本文我们将使用是demo版本，可以从[这里](http://www.hopperapp.com/download.html)下载。

下载完成之后，解压安装。


###反汇编举例

我们编写一个demo，在ViewDidLoad中调用UIAlertView，代码如下：

    - (void)viewDidLoad
    {
        [super viewDidLoad];
        
        UIAlertView *alert =
        [[UIAlertView alloc] initWithTitle:@"Welcome"
                                   message:@"Welcome to your iOS Device Ted!"
                                  delegate:nil
                         cancelButtonTitle:@"security.ios-wiki.com"
                         otherButtonTitles:nil];
        
        [alert show];
        
    }
    

![](https://farm4.staticflickr.com/3806/14291712205_cdbb3921e7_c.jpg)

编译运行，使用[SimPholders](http://simpholders.com/)，点击其中的AlertDemo

![](https://farm3.staticflickr.com/2904/14311893343_32bd112dc6.jpg)

会打开目录中选择AlertDemo右键，点击Show Package contents
![]()

然后用Hopper打开里面的可执行文件

![](https://farm3.staticflickr.com/2915/14105079240_30cb2d2c2b_c.jpg)

可以看到ViewDidLoad中的汇编代码如下：

![](https://farm4.staticflickr.com/3703/14105058508_299a79d055_b.jpg)

点击右上角箭头所指的图标，得到如下的伪代码：

![](https://farm4.staticflickr.com/3786/14291190544_7b8ce5d823_b.jpg)


可以看到，用Hopper能够得到近似源码的伪代码。


    function -[ViewController viewDidLoad] {
        _PIC_register_ = eax;
        var_44 = @"Welcome to your iOS Device Ted!";
        var_40 = @"security.ios-wiki.com";
        var_72 = arg_offset_x0;
        var_68 = arg_offset_x4;
        var_56 = var_72;
        var_60 = *0x3570;
        var_36 = @"Welcome";
        var_32 = &var_52;
        var_28 = 0x0;
        [[&var_56 super] viewDidLoad];
        eax = [UIAlertView alloc];
        var_52 = objc_msgSend(eax, *0x3558);
        [var_52 show];
        eax = objc_storeStrong(var_32, 0x0);
        return eax;
    }






####小结

可以看到，Hopper功能相当强大，它还有更多功能，欢迎读者亲自去尝试下，去买一个正版，价格也比IDA便宜得多。




***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)

