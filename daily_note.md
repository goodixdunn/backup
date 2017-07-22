#Daily note

[TOC]

##工具类
###git
1. 安装
	sudo apt-get install git
2. 常用配置
	git config --global user.name "dunn"
	git config --global user.email "uestc2013"
	git config --global core.editor "vim"
3. [产生秘钥并且添加到github账号的方法](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)
4. 若clone时使用https协议，push时[免输入密码的方法](https://help.github.com/articles/caching-your-github-password-in-git/) ，建议clone时，使用git协议，这样配置了github的ssh后就不用输入密码了
4. 常用命令介绍
>  git push -f origin master：强制将本地提交到远程
###sublime
1. 安装
	[按照官网的命令行方式安装](https://www.sublimetext.com/)
2. 插件
	cscope
	ctags：需要先安装ctags和cscope
	SidebarEnhancements
	SublimeLinter
	SublimeCodeIntel
	git
	git gutter
	emment
	AllAutocomplete
	Terminal
	DocBlockr
	AutoFileName
	TrailingSpaces
3. 常见问题
	- 改变默认字体，适用于编程-等间距
		下载[Source Code Pro](https://github.com/adobe-fonts/source-code-pro)  ，这个是adobe公司开发的，下载字体后，直接打开以otf或者ttf结尾的文件，会自动打开字体查看器，然后点击安装。
		安装后，在settings中添加："font_face": "Courier New",
###oh-my-zsh
1. [安装](This repository) ，安装后在～生成.oh-my-zsh以及.zshrc文件
2. 安装完成以后，需要修改默认bash：chsh -s /bin/zsh
3. 更改主题为apple后，需要下载并安装特殊[fonts](https://github.com/powerline/fonts)

###ultraEdit
1.  [安装](http://pan.baidu.com/s/1jGoY43k)
##提高效率
1. 快速显示桌面
	系统设置->外观->行为
2. firefox同步账号
	uestc2013@163 + woaishuishuixxxx
3. 右键终端
	sudo apt-get install nautilus-open-terminal
4. vim
	sudo apt-get install vim
5. ag
	sudo apt-get install silversearcher-ag
6. 命令行滚动限制取消以及透明背景
##知识汇总
- C/C++/JAVA
- make/bash/gcc/gdb/sed/awk
- 设计模式
- 数据库
##libevent
1. 从github  clone源码，获取[安装方法](https://github.com/libevent/libevent)
2. 运行 autogen.sh，需要先安装：
	sudo apt-get install autotools-dev
	sudo apt-get install automake
	sudo apt-get install autogen autoconf libtool
3.
