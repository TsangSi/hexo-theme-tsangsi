---
title: "[cocos2d-x 3.x] 未安装未知的luaJIT命令或jit。*模块"
catalog: true
toc_nav_num: true
date: 2019-10-11 10:51:24
subtitle: "未安装未知的luaJIT命令或jit。*模块"
header-img: "/img/article_header/article_header.png"
tags:
- cocos2d-x 3.x
catagories:
- cocos2d-x 3.x
updateDate: 2019-10-09 22:26:24
top: 1

---
> This Tsang Si theme created by [Tsang Si](https://tsangsi.github.io/) modified from the original Porter [YuHsuan](https://github.com/YenYuHsuan/hexo-theme-beantech)

# 要注意luajit.exe和源代码src / jit文件夹要同时和匹配使用。

D：\ cocos2d-x-3.2 \ cocos2d-x-3.2 \ tools \ cocos2d-console \ plugins \ plugin_luacompile \ bin \ lua把这文件下的jit文件夹复制出来跟luajit.exe同个路径下

下面的是    
新版本的cocos2d-x使用了luajit来替代原始的lua，好处一是可以极大的提高运行速度（android下可以开jit，运行速度提高10〜60倍，ios下不可以开jit，运行速度也可以提高2〜3倍。。二是luajit编译的字节码现阶段来说无法反编译，从而无法破解。这里对luajit进行详细的说明。

 
       1，基本使用。这个很简单，不用修改任何代码，与lua5.1完全兼容。对应的头文件和库要替换成luajit的（与lua命名相同）
 
       2，发布时编译成字节码。
 
             编译方法（windows）：在（http://luajit.org/download.html）下载源代码，使用vs的命令行工具，执行ms vc .bat进行编译。开始使用cygwin直接执行make结果出了很多编译错误和诡异的问题。实际上直接使用批处理才是正确的方式。 
 
             编译完成后，要注意luajit.exe和源代码src / jit文件夹要同时和匹配使用。否则运行时会出现“未知的luaJIT命令或jit。*模块未安装”的错误。
 
            编译成字节码的命令如下：luajit -b原始文件输出文件
 
            更多详细说明可以直接执行luajit -b来获取。这里还要注意，输出文件最好保持.lua的扩展名，否则需要文件的时候会提示找到文件。除非需要里面使用的完整名字。
 
           我在编译字节码时还碰到了一个问题，就是无论编译什么文件都提示这个“ luajit'='预期在'<eof>'附近”“，后来换了luajit的版本后就没有出现了。可能跟我一开始手欠改了luajit源代码有关。
 
       3，编译luajit的静态库文件（适用于android）
 
            编译字节码所用的执行文件和我们开发程序时使用的静态库文件要保持版本一致。Windows版本所需要的文件在编译执行文件时就已经生成。
 
            android编译需要使用NDK在windows下进行交叉编译。此处我一开始使用cygwin没有成功。一大堆编译错误。后来换mingw就相对顺利了。我使用的是msys + mingw的环境（在装msysgit时附带装好的..）编译脚本参考了cocos2d-x本身的和luajit官网的。
	
 </script>
NDK指定的是android ndk的安装目录。原本cocos2d-x的脚本自动检测主机（与这行代码相关NDKP）的最开始设置好SRCDIR和DIR这个是Luajit源代码的目录和编译好的库文件的安装目录。 = $ NDKVER / prebuilt / windows / bin / arm-linux-androideabi-），但是不太管用，所以统一修改为windows
 
这里还要注意下，我在编译armv7a的时候总是出现minilua.exe执行错误，无法编译成功，去掉-mfloat-abi = softfp后编译正常。
# Have fun ^_^ 
---
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->

Please <a class="github-button" href="https://github.com/tsangsi/hexo-theme-tsangsi" data-icon="octicon-star" aria-label="Star tsangsi/hexo-theme-tsangsi on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/tsangsi" aria-label="Follow @tsangsi on GitHub">Follow</a> would also be appreciated!
Peace!
