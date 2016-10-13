---
layout: post
title: "iTerm2 Configuration"
date: 2016-1-11
image: /images/2016/1/11/E15C793C-161A-4F38-9D67-F7C6B10B8338.jpg
image-sm: /images/2016/1/11/E15C793C-161A-4F38-9D67-F7C6B10B8338.jpg
categories:
 - 笔记
---

#### 配置iTerm2 主题

感觉iTerm2默认不是很漂亮，而且对于一些特殊的文件也没有高亮显示，折腾一番最终完成，看下成果：
![][1]

#### 安装iTerm2
如果没有[iTerm2](http://iterm2.com/),请先安装下载地址。

#### 具体配置

[具体配置步骤请参考这位同学，已经写的非常详尽](https://laoshuterry.gitbooks.io/mac_os_setup_guide/content/4_ZshConfig.html)

#### 问题与解决方案

![][2]

> 图标显示为问号，这个问题是系统的字体导致的，解决方案是参考这里的[issues](https://github.com/robbyrussell/oh-my-zsh/issues/4756#event-508955141)

具体解决方案是：

* 按`cmd + ,`打开偏好设置
* 选择`Profiles`,选择`Text`选项
* 更改字体为以`for Powerline`后缀结尾的字体
* 更改完成问号立刻恢复正常标签

[1]:/images/2016/1/11/E15C793C-161A-4F38-9D67-F7C6B10B8338.jpg
[2]:/images/2016/1/11/5AA27942-AF82-4CB4-961C-191DB36F1C50.png
