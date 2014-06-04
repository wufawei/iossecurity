所有发布的iOS设备都是基于ARM架构的。我们开发iOS应用的时候编写的Objective-C代码会首先转换成ARM汇编，然后转换成机器指令。对ARM汇编语言和使用GDB调试有很好掌握的话，攻击者是能够在运行时解密Objective-C代码甚至修改代码的。


####下载

你可以到这里[https://code.google.com/p/apiexplorer/downloads/detail?name=gdb-1821.deb](https://code.google.com/p/apiexplorer/downloads/detail?name=gdb-1821.deb) 下载到你的Mac上，然后使用iFunbox拷贝到你越狱之后的iOS设备上。

然后SSH进iOS设备：

![](https://farm4.staticflickr.com/3668/14307445592_e81e438c31_c.jpg)

转到gdb-1821.deb所在的文件夹，然后执行：

	dpkg -i gdb-1821.deb 

如图：

![](https://farm6.staticflickr.com/5524/14122736240_f13f61367d_c.jpg)

     


####用法简介

请注意，请先点击要分析的App，使得App处于运行状态。然后在命令行输入命令：
     
    ps aux
    
可以得到正在运行的App的pid等信息。你也可以过滤出你想要的信息，比如：

    ps aux | grep 'Momo'
    
可以得到你要分析的应用（如Momo）的pid。如下图所示：
![](https://farm4.staticflickr.com/3743/14305984721_f5b0e86fb5_b.jpg)

然后输入：

    gdb --pid 5413 (你的pid可能跟这不同，请注意替换)
  
  这样你就挂钩进App了。一旦GDB挂钩进了这个应用，你会注意到这个应用目前是在暂停状态。你可以用 c 命令让这个应用继续执行, 如图：
  
  ![](https://farm3.staticflickr.com/2908/14122660589_afd99e83ce.jpg)
  
  不过在继续执行之前，让我们先做些调查。和任何其它架构一样，ARM中的内存也被分为寄存器（register）。所有的寄存器都是32位的（iOS 7中是64位的），并且它们的目的就是保存数据。你可以使用 info registers命令来查看关于这些寄存器的信息。
  
  
![](https://farm4.staticflickr.com/3833/14307447602_609fb02b02_c.jpg)

请注意这个命令并没有把ARM中的所有寄存器都打印出来。要打印所有的寄存器，使用 info all-registers命令。

有如下info命令：

![](https://farm4.staticflickr.com/3668/14286184126_3b98c3ca84_c.jpg)

执行下info stack试试：

![](https://farm4.staticflickr.com/3817/14286184756_bb803096ed_c.jpg)


要导出汇编信息，使用disassemble 或者 disas 命令。这会给出后续几条指令的一些汇编信息。我们通过在
disas命令后面提供函数名称来导出某个特定函数的汇编。例如要导出main函数的汇编，使用命令 

    disas
    
   或者
   
    disassemble
   

如下图：

![](https://farm6.staticflickr.com/5153/14286185996_1db841e1a7_c.jpg)


####小结

本节我们简要介绍了如何安装和加载GDB，后面会在关于对iOS应用进行动态分析的章节会详细介绍其用法。

***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)
