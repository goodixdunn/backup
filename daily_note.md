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
###oh-my-zsh
1. [安装](This repository) ，安装后在～生成.oh-my-zsh以及.zshrc文件
2. 安装完成以后，需要修改默认bash：chsh -s /bin/zsh
3. 更改主题为apple后，需要下载并安装特殊[fonts](https://github.com/powerline/fonts) 
	
###ultraEdit
##提高效率
1. 快速显示桌面
	系统设置->外观->行为
2. firefox同步账号
	uestc2013@163 + woaishuishuixxxx
3. 右键终端
	sudo apt-get install nautilus-open-terminal
4. vim
	sudo apt-get install vim
5. 