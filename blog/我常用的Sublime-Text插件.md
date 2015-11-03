title: "我常用的Sublime Text插件"
date: 2015-04-21 21:26:39
tags: Sublime
---

Sublime Text是非常有逼格的一个编辑器，有各种丰富的插件，可以打造成顺手IDE，今天就总结一下我常用的一些插件。

今年四月份的时候刚刚抛弃了ST2，拥抱ST3~
### Package Control
Package Control是用来安装各个插件的一个工具。安装方法如下：

按Ctrl + `，在弹出的框内粘贴如下一段代码，然后Enter，等待安装完成即可。
``` xml
import urllib.request,os,hashlib; h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
待安装完成后按Ctrl + Shift + P,在弹出框内输入install找到Package Control:Install Package 功能项，进入，即可搜索安装需要安装的插件了

更多有关安装Package Control的信息，点击[这里](https://packagecontrol.io/installation)

### Python Anaconda
开发Python的必备利器。

### Terminal
在Sublime Text中打开一个terminal终端，快捷键是 Ctrl + Shift + T

### JsFormat
一个格式化JS代码的插件。

### Emmet
Sublime Text的Zen Coding插件。

用法：

1. 首先 Ctrl + Shift + P，输入sshtml，设置页面为html格式
2. 输入html标签，如 html ，然后按tab键即可。
3. 几种常用的Zen Coding写法：

### HTML-CSS-JS Prettify
格式还html css和js代码。
默认快捷键：**Ctrl + Shift + H**。或者右键选择“HTML/CSS/JS Prettify” -> "Prettify Code"

### OmniMarkupPreviewer
刚开始使用，可以在浏览器中预览Markdown格式（Ctrl + Alt + O），可以导出到HTML格式，更多功能还在摸索。

### 常用的快捷键

Ctrl + B: build python代码
Ctrl + /: 注释代码
Ctrl + Shift + H：格式化html/css/js代码
