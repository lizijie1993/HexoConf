title: ubuntu常用软件安装记录
comments: true
date: 2015-07-09 23:37:14
updated:
categories: 工具分享
tags: ubuntu 软件
---
本人安装的为64位ubuntu14.04。

## 搜狗拼音
转自[百度经验 Ubuntu14.04安装搜狗输入法](http://jingyan.baidu.com/article/ad310e80ae6d971849f49ed3.html)

1. [搜狗拼音官网]("http://pinyin.sogou.com/linux/?r=pinyin")下载系统适用版本；
2. 双击下载的 *.deb* 包，进入软件中心安装；
3. 终端输入im-config，点击 *OK* 、 *YES* ,然后选择 *fcitx*，点击 *OK*；
4. 重启电脑；
5. 终端输入 `fcitx-config-gtk3`，点击左下角的 *+*，取消 *Only Show Current Language* 前的勾，找到刚刚安装的 sogou拼音，点击OK。

## Sublime Text 3

1. [Sublime Text 3 官网](http://www.sublimetext.com/3)下载对应版本的安装包（.deb）；
2. 双击下载的 `.deb` 包，进入软件中心安装；
3. 安装各类插件，见[]()；
4. 使其支持输入中文，方法如下：

### Ubuntu下支持Sublime Text 3输入中文
转自
[百度经验 Ubuntu下Sublime Text 3解决无法输入中文的方法](http://jingyan.baidu.com/article/f3ad7d0ff8731609c3345b3b.html)

1. 在某一目录下，新建 *sublime_imfix.c* 文件，输入以下内容：

	```c
	#include <gtk/gtkimcontext.h>
	void gtk_im_context_set_client_window (GtkIMContext *context,
	         GdkWindow    *window)
	{
	 GtkIMContextClass *klass;
	 g_return_if_fail (GTK_IS_IM_CONTEXT (context));
	 klass = GTK_IM_CONTEXT_GET_CLASS (context);
	 if (klass->set_client_window)
	   klass->set_client_window (context, window);
	 g_object_set_data(G_OBJECT(context),"window",window);
	 if(!GDK_IS_WINDOW (window))
	   return;
	 int width = gdk_window_get_width(window);
	 int height = gdk_window_get_height(window);
	 if(width != 0 && height !=0)
	   gtk_im_context_focus_in(context);
	}
	```

2. 将上一步的代码编译成共享库 *libsublime-imfix.so*，命令：

	``gcc -shared -o libsublime-imfix.so sublime_imfix.c  `pkg-config --libs --cflags gtk+-2.0` -fPIC``  

	注：需要先使用 `sudo apt-get install` 安装 *gcc*、*gtk+-2.0*

3. 将 *libsublime-imfix.so* 拷贝到 *sublime_text* 所在文件夹：

	`sudo mv libsublime-imfix.so /opt/sublime_text/`

4. 修改文件 */usr/bin/subl* 的内容：

	`sudo gedit /usr/bin/subl`  
	将
	`#!/bin/sh  
	exec /opt/sublime_text/sublime_text "$@"`  
	修改为  
	`#!/bin/sh  
	LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text "$@"`  

5. 修改文件sublime_text.desktop的内容：

	`sudo gedit /usr/share/applications/sublime_text.desktop`  
	将[Desktop Entry]中的字符串  
	`Exec=/opt/sublime_text/sublime_text %F`  
	修改为  
	`Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text %F"`  
	将[Desktop Action Window]中的字符串  
	`Exec=/opt/sublime_text/sublime_text -n`  
	修改为  
	`Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text -n"`  
	将[Desktop Action Document]中的字符串  
	`Exec=/opt/sublime_text/sublime_text --command new_file`  
	修改为  
	`Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text --command new_file"`  
	注意：  
	修改时请注意双引号"",否则会导致不能打开带有空格文件名的文件。  
	此处仅修改了 /usr/share/applications/sublime-text.desktop ，但可以正常使用了。  
	opt/sublime_text/ 目录下的 sublime-text.desktop 可以修改，也可不修改。  
