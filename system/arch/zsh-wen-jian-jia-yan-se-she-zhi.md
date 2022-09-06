# zsh 文件夹颜色设置



1. 在\~/.zshrc中添加：

```
if [ -x /usr/bin/dircolors ]; then
	test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
	alias ls='ls --color=auto'
	#alias dir='dir --color=auto'
	#alias vdir='vdir --color=auto'
	alias grep='grep --color=auto'
	alias fgrep='fgrep --color=auto'
	alias egrep='egrep --color=auto'	
fi
```

1. cd \~，dircolors -p > .dircolors，修改 \~/.dircolors 中对应文件夹类别的颜色
2. source \~/.zshrc
