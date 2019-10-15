---
title: "[cocos2d-x 3.x] cocos2d-x 3.2 打包Android平台apk"
catalog: true
toc_nav_num: true
date: 2019-10-11 10:51:24
subtitle: "配置环境，打包成apk"
header-img: "/img/article_header/article_header.png"
tags:
- cocos2d-x 3.x
catagories:
- cocos2d-x 3.x
updateDate: 2019-10-09 22:26:24
top: 1

---

从cocos2dx 3.2项目打包成apk安卓应用文件，构建安卓环境的步骤有点繁琐，但建造一次之后，以后就会非常快捷！

（涉及到3.1.1版本的，请自动对应3.2版本，3.x版本的环境构造都是一样的）

#一，下载Android环境

搭建Android环境需要用到Android SDK，NDK，Ant和JDK；

#####Android SDK下载http://developer.android.com/sdk/index.html

#####Android NDk下载http://developer.android.com/tools/sdk/ndk/index.html

#####Android JDK下载http://www.oracle.com/technetwork/java/javase/downloads/index.html

#####蚂蚁下载http://ant.apache.org/bindownload.cgi

将SDK NDK和ANT解压缩到同一个文件夹中，我解压后文件都放在了e：\ Android文件夹下

 

SDK如果下载下来的adt文件那么把里面的SDK文件夹考出来，修改后

 

#二，配置

在JDK安装完毕之后，需要手动进行环境变量的配置

####1，系统变量里新建JAVA_HOME变量，变量变量：C：\ Program Files \ Java \ jdk1.8.0_05（根据自己的安装路径填充）

####2，新建系统变量classpath变量，变量变量：

#####`。;％JAVA_HOME％\ lib;％JAVA_HOME％\ lib \ tools.jar`（注意前面的。不要忘记了）

####3，在系统变量path变量（已存在不用新建）添加变量值：

#####`％JAVA_HOME％\ bin;％JAVA_HOME％\ jre \ bin`（注意变量值之间用“;”替换）

####环境变量的解析：

#####JAVA_HOME：jdk的安装路径

#####classpath：java加载类路径，只有类在classpath中java命令才能识别，在路径前加了个“。”表示当前路径。

#####path：系统在任何路径下都可以识别java，javac命令。

在配置完成之后，我们打开cmd命令窗口，输入java或javac回车，如果不是打印“ java不是内部或外部命令”那就说明配置成功了。

 

 

 



#三，配置Ant环境变量

#####把ant的存放路径加入系统Path变量。

 



#四，配置cocos2dx 3.1 Android环境关联

以上已做好准备工作，打开cocos2dx目录下的setup.py。

 

输入ndk的路径。

 

输入sdk路径

 

最后输入的是ant的路径

 

OK，三个路径都已经配置完毕

 

 

#五，打包Apk

然后就可以在项目目录下使用cocos compile -p android方式编译APK文件了。

注意：在打包apk之前需要修改proj.android/jni文件夹下的Android.mk文件添加自己添加的CPP文件，这里的添加指的是非cocos2dx生成的项目文件（自己添加的文件）。一定要把自己补充的文件或文件夹包含进apk。

首先我们拿官方的cpp-tests项目来试手吧！

按住Shift键+鼠标右键选择在此打开命令窗口。

 

 

然后就可以使用`cocos compile -p android --ap 20`方式编译APK文件了（--ap 20是指定android平台）

 

漫长的等待吧，当然这里要提醒一点3.1版本用的是android-20，如果E：\ Android \ sdk \ platforms目录下没有android-20请创建一个空的。

 

等待完毕之后看下这个文件夹下的apk吧！可以安装到手机上试一下了。

 

#六，异常处理：（我会不定时的更新遇到的问题，让大家都不走弯路）

####1，未指定Android平台，正在搜索默认平台...

找不到适合项目的android平台：

“ F：\ cocos2d-x-3.1.1 \ tests \ cpp-tests \ proj.android”。

android-platform应该等于/大于20

如果你遇到这个问题，那么就要在



下创建一个空的android-20文件夹，这个问题坑了我很久！同时要查看命令是否是–ap 20（双-）。

检查\ proj.android目录下的project.properties是否是android-20

 

 

####2，编译时，提示找不到gcc工具链（找不到gcc工具链）。

这个问题也烦了我很久！去检查下ndk的配置路径！NDK_ROOT是否正确！然后3.1版本需要的NDK是最新的版本，

去安卓网站http://developer.android.com/tools/sdk/ndk/index.html#Installing下一个最新的版本r9d吧！

####3，如果遇到android api问题



 

找到运行进行更新，看缺少其中api就更新其中，不过这个更新比较慢……导致Google被禁……

####4，Android NDK：无效的APP_STL值：c ++ _ static这个要修改jni / Application.mk

    APP_STL：= c ++ _ static
    
    NDK_TOOLCHAIN_VERSION = c
    
    APP_CPPFLAGS：= -frtti -DCC_ENABLE_CHIPMUNK_INTEGRATION = 1 -std = c ++ 11 -fsigned-char
    
    APP_LDFLAGS：= -latomic

改成：

    APP_STL：= gnustl_static
    
    ＃添加-Wno-literal-suffix以避免警告：warning：字面量后缀无效；C ++ 11在文字和标识符之间需要一个空格[-Wliteral-suffix] 
    
    ＃在NDK_ROOT / arch-arm / usr / include / sys / cdefs_elf.h：35：28中：使用ndk-r9时
    
    APP_CPPFLAGS：= -frtti -DCOCOS2D_DEBUG = 1 -std = c ++ 11 -Wno-literal-suffix -fsigned-char

####5，Android NDK：警告：APP_PLATFORM android-14大于android：minSdkVersion 8 

这是因为NDK指定的版本与minSdkVersion重复的问题，修改了AndroidManifest.xml文件，其中android：minSdkVersion修改为`<uses-sdk android：minSdkVersion =“ 20” />`

# Have fun ^_^ 
---
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->

Please <a class="github-button" href="https://github.com/tsangsi/hexo-theme-tsangsi" data-icon="octicon-star" aria-label="Star tsangsi/hexo-theme-tsangsi on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/tsangsi" aria-label="Follow @tsangsi on GitHub">Follow</a> would also be appreciated!
Peace!
