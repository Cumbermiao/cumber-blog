原文链接：[https://segmentfault.com/a/1190000011008535](https://segmentfault.com/a/1190000011008535)

windows下终端神器之Cmder
==================

前言
==

在windows下面其实大家很少用cmd，也很少人专门去学bat编程，实在是太不方便。那么一个黑糊糊的小窗口，界面比小霸王学习机还土，不能随意改变尺寸, 不支持多tab。久而久之，也没人关心在windows下面如何像linux下的terminal那样操作了。在网上瞎逛时，偶尔看到有人提及cmder和conemu才知道有个近似linux下的bash的东西。装上cmder试了一下果然很酷，所以写点东西来推广一下。

安装
==

安装cmder有两种包可选择，一种是mini, 一种是full. cmder其实是包装了几种软件，主要是clink, git, conemu, 如果你要自己装git就可以选择mini, 否则安装full。  
这里是[官方主页](http://cmder.net/), 下载地址就在主页靠中间的位置。  
建议安装full, 然后直接解压缩就可以用了  
如果是安装的mini包，那么这里需要配置Git的安装目录。  
因为我的Git是安装在D盘的Program Files下的，跨盘同时路径中有空格，我折腾了好久都没法在cmder的setting里配置好Task->bash的路径  
后面想了个完美方法，就是在cmder安装目录下的vendor文件夹里建一个软链接到git的安装目录里  
win7下可以用下面命令

    C:\cmder\vendor> mklink /D git-for-windows "D:\Program Files\Git"
     symbolic link created for git-for-windows <<===>> D:\Program Files\Git

配置
==

路径
--

把cmder的目录加入到系统PATH里  
右键我的电脑->属性->高级->环境变量->PATH->Edit  
把cmder的目录添加到系统PATH里  
![图片描述](https://segmentfault.com/img/bVUlYF?w=421&h=496 "图片描述")

右键注册
----

为了右键菜单有从这里打开cmder, 可以执行下面操作  
打开administor权限的cmd.exe  
![图片描述](https://segmentfault.com/img/bVUlYI?w=281&h=189 "图片描述")  
在打开的cmd.exe里执行下面命令  
![图片描述](https://segmentfault.com/img/bVUlYJ?w=677&h=392 "图片描述")

执行完成之后，右键菜单如下  
![图片描述](https://segmentfault.com/img/bVUlYO?w=463&h=381 "图片描述")

ls中文支持
------

默认安装好后，ls命令会显示中文乱码，需要打开setting窗口(win+alt+p)，设置LANG  
![图片描述](https://segmentfault.com/img/bVUlYV?w=766&h=489 "图片描述")

界面
--

打开cmder的窗口如下  
![图片描述](https://segmentfault.com/img/bVUlYX?w=992&h=559 "图片描述")  
可以看到界面非常舒服，并有不同颜色显示

提示符修改
-----

如果喜欢用$做提示符，找到vendor/clink.lua

    cmder_prompt = string.gsub(cmder_prompt, "{cwd}", cwd)
    if env == nil then
        lambda = "λ"
    else
        lambda = "("..env..") λ"
    end
    clink.prompt.value = string.gsub(cmder_prompt, "{lamb}", lambda)
    end

把上面的λ替换成$重启cmder就行了，我觉得无所谓，所以没改

使用
==

快捷键
---

cmder的快捷键和浏览器的很相近，如果要用爽cmder一定记住常用快捷键

*   双击Tab, 可以路径补全
*   可以用Ctrl+T新建Tab页
*   利用Ctrl-W关闭当前Tab页
*   使用Ctrl-n来切换到第n个Tab页
*   Alt+Enter切换到全屏
*   Alt+Shift+n开启你的settings->Tab下的terminals
*   Ctrl-r反向搜索历史命令
*   Win+Alt+p打开设置窗口
*   Ctrl-u 删除文字到行首
*   Ctrl-A 移动光标到行首
*   Ctrl-E 移动光标到行尾

操作
--

*   双击选中，然后右键粘贴
*   左键选中文字， Ctrl-V粘贴

问题
==

ls时快时慢
------

ls是系统中用的最多的命令，但不幸的是，当我用softlink在vendor目录里建立到Git的链接后，在Cmder里输入ls后的返回时快时慢，慢的时候大于10秒，这绝对是不能容忍的。

### 解决方法一

后来我从u-tools里下载了windows下的ls， 地址为[https://u-tools.com/download\_...](https://u-tools.com/download_prod.asp?download%5Fid=600&Agree=1)  
解压后放到bin里，这样就没有问题了。  
不过ls的输出有时不对齐  
![图片描述](https://segmentfault.com/img/bVUqAR?w=672&h=288 "图片描述")

### 解决方法二

再后来我把Git卸载重装后，ls的问题也好了。 Git里的command比msys2里的还新，用msys2里的ls替换Git里的也不会有ls返回时快时慢的问题  
这个好像过一段时间又会出问题

### 解决方法三

可能和系统权限有关系，所以有下面的方法

    mkpasswd -l -c > C:\cmder\vendor\git-for-windows\etc\passwd
    mkgroup -l -c > C:\cmder\vendor\git-for-windows\etc\group

打开 C:cmdervendorgit-for-windowsetcnsswitch.conf

      # Begin /etc/nsswitch.conf
    
      passwd: files # db
      group: files # db
    
      db_enum: cache builtin
    
      db_home: env windows cygwin desc
      db_shell: env windows # cygwin desc
      db_gecos: env # cygwin desc
    
      # End /etc/nsswitch.conf

主要是把group和passwd后面的db给注释掉

遗留问题
====

如果真的作为ssh终端工具，那么一个很严重的问题已经无法支持一些特殊字符，比如vim-airline的那些字符，或者是vim的一些theme, 希望以后cmder在这方面有大的改进

后言
==

cmder集成了很多linux的命令以及git，使用起来非常方便。有了它，基本也可以像Linux下一样，利用它做很多操作和控制工作。具体的用法还需要大家自己去摸索，我在这里只是抛砖引玉，希望大家可以找到它的更多用处。  
我找到的一个用途是用它来ssh到服务器。我觉得目前在windows上最好的ssh工具是xshell，不幸地是它不能用于商业用途，而Cmder可以凑和用作windows下的ssh工具，结合tmux的会话保存功能，还是很爽的。