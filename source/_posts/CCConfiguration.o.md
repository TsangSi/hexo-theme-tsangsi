---
title: "[cocos2d-x 3.x] make: *** No rule to make target ...CCConfiguration.o Stop."
catalog: true
toc_nav_num: true
date: 2015-5-30 14:45:00
subtitle: "编译Coco2d-x android 程序出现make: *** No rule to make target ...CCConfiguration.o'. Stop."
header-img: "/img/article_header/article_header.png"
tags:
- cocos2d-x 3.x
catagories:
- cocos2d-x 3.x
updateDate: 2015-5-30 14:45:00
top: 1

---

在eclipse中编译cocos2d-x的项目可能出现这个问题。也许是你对项目没有clean，然后就让他编译造成的。

 

具体错误可能显示为：

`make: *** No rule to make target '/cygdrive/d/android/cocos2d-x-master/T12/proj.android/../../cocos2dx/CCConfiguration.cpp', needed by 'obj/local/armeabi/objs/cocos2dx_static/CCConfiguration.o'. Stop.`

 

从这个问题可以看出，可能跟CCConfiguration.o有关，那么找到这个文件，怎么找呢？

直接在cocos2d-x根目录搜索吧。（我安装在D:\AndroidDev\cocos2d-2.0-x-2.0.4）

好，找到了，发现在

`D:\AndroidDev\cocos2d-2.0-x-2.0.4\cocos2d-2.0-x-2.0.4\Cocos2dxSimpleGame\proj.android\obj\local\armeabi`

目录下有个`objs-debug/cocos2dx_static`文件夹。

 

进去正好有个`CCConfiguration.o`文件。从后缀名来看，这个肯定是编译中间文件（可以删除）。

而Make 的No rule to make target则一般说明是已经存在了，不用再编译。

 

#####所以我们把objs-debug文件夹下面的文件（包括文件夹）全删除。

 

#####再次在eclipse里面clean一下，就发现可以编译了。

# Have fun ^_^ 
---
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->

Please <a class="github-button" href="https://github.com/tsangsi/hexo-theme-tsangsi" data-icon="octicon-star" aria-label="Star tsangsi/hexo-theme-tsangsi on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/tsangsi" aria-label="Follow @tsangsi on GitHub">Follow</a> would also be appreciated!
Peace!
