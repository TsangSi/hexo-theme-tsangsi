---
title: "[Lua] lua 从字符串获取指定第几位数到第几位数"
catalog: true
toc_nav_num: true
date: 2015-6-12 11:29:00
subtitle: ""
header-img: "/img/article_header/article_header.png"
tags:
- Lua
catagories:
- Lua
updateDate: 2015-6-12 11:29:00
top: 1

---
    -- 判断utf8字符byte长度
    -- 0xxxxxxx - 1 byte
    -- 110yxxxx - 192, 2 byte
    -- 1110yyyy - 225, 3 byte
    -- 11110zzz - 240, 4 byte
    local function chsize(char)
		if not char then
		    print("not char")
		    return 0
		    elseif char > 240 then
		    return 4
		    elseif char > 225 then
		    return 3
		    elseif char > 192 then
		    return 2
		    else
		    return 1
	    end
    end
    
    -- 计算utf8字符串字符数, 各种字符都按一个字符计算
    -- 例如utf8len("1你好") => 3
    function utf8len(str)
	    local len = 0
	    local currentIndex = 1
	    while currentIndex <= #str do
		    local char = string.byte(str, currentIndex)
		    currentIndex = currentIndex + chsize(char)
		    len = len +1
	    end
	    return len
    end
    
    -- 截取utf8 字符串
    -- str: 要截取的字符串
    -- startChar:   开始字符下标,从1开始
    -- numChars:要截取的字符长度
    function utf8sub(str, startChar, numChars)
	    local startIndex = 1
	    while startChar > 1 do
		    local char = string.byte(str, startIndex)
		    startIndex = startIndex + chsize(char)
		    startChar = startChar - 1
	    end
    
	    local currentIndex = startIndex
	    
	    while numChars > 0 and currentIndex <= #str do
		    local char = string.byte(str, currentIndex)
		    currentIndex = currentIndex + chsize(char)
		    numChars = numChars -1
	    end
	    return str:sub(startIndex, currentIndex - 1)
    end
    
    -- 自测
    function test()
	    -- test utf8len
	    assert(utf8len("你好1世界哈哈") == 7)
	    assert(utf8len("你好世界1哈哈 ") == 8)
	    assert(utf8len(" 你好世 界1哈哈") == 9)
	    assert(utf8len("12345678") == 8)
	    assert(utf8len("øpø你好pix") == 8)
    
	    -- test utf8sub
	    assert(utf8sub("你好1世界哈哈",2,5) == "好1世界哈")
	    assert(utf8sub("1你好1世界哈哈",2,5) == "你好1世界")
	    assert(utf8sub(" 你好1世界 哈哈",2,6) == "你好1世界 ")
	    assert(utf8sub("你好世界1哈哈",1,5) == "你好世界1")
	    assert(utf8sub("12345678",3,5) == "34567")
	    assert(utf8sub("øpø你好pix",2,5) == "pø你好p")
	    
	    print("all test succ")
    end
    
    test()
# Have fun ^_^ 
---
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->

Please <a class="github-button" href="https://github.com/tsangsi/hexo-theme-tsangsi" data-icon="octicon-star" aria-label="Star tsangsi/hexo-theme-tsangsi on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/tsangsi" aria-label="Follow @tsangsi on GitHub">Follow</a> would also be appreciated!
Peace!
