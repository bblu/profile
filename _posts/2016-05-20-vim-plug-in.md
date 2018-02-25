---
title: 使用Vundle管理vim的插件
date: 2016-05-20
categories: blog
tags: [vim,shell]
---
Vundle利用git,插件的git repo以及vim-scripts维护的GitHub repo, 自动安装, 更新和卸载插件. 它把这些繁杂的工作变得简单, 甚至, 成为一种享受.

### 安装
Vundle的github地址在这里,上面有安装和使用方法,我在这里也简单的写一下
1git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

### 配置见最后
vim ~/.vimrc

### 使用
前面非常简单的步骤已经安装好了,安装NERDTree:
  * 在github上搜索nerdtree找到它,我找到的是: https://github.com/scrooloose/nerdtree
  * 在～/.vimrc文件里添加一行:
1Bundle 'scrooloose/nerdtree'
  * 退出并重新打开VIM,执行:BundleInstall，或者在命令行里面运行：vim +BundleInstall +qa
新的外挂NERDTree已经安装好了,可以在~/.vim/bundle/目录下看到它

### 我的配置信息

``` bash
#符号注释的原因这个不能直接用要把#换成引号，也可以到最后下载我的配置
set nocompatible              # be iMproved, required
 filetype off                  # required
 
 # set the runtime path to include Vundle and initialize
 set rtp+=~/.vim/bundle/Vundle.vim/
 call vundle#begin()
 
 # let Vundle manage Vundle, required
 Plugin 'gmarik/Vundle.vim'
 # Plugins
 Plugin 'Valloric/YouCompleteMe'
 Plugin 'scrooloose/syntastic'
 Plugin 'SirVer/ultisnips'
 Plugin 'edsono/vim-matchit'
 Plugin 'elzr/vim-json'
 Plugin 'honza/vim-snippets'
 Plugin 'kien/ctrlp.vim'
 Plugin 'ludovicchabant/vim-lawrencium'
 Plugin 'mhinz/vim-signify'
 Plugin 'plasticboy/vim-markdown'
 Plugin 'scrooloose/nerdcommenter'
 Plugin 'sjl/gundo.vim'
 Plugin 'tpope/vim-surround'
 Plugin 'tpope/vim-fugitive'
 Plugin 'FuzzyFinder'
 
 # Color schemes
 Plugin 'tomasr/molokai'
 Plugin 'flazz/vim-colorschemes'
 # Required, plugins avalable after.
 call vundle#end()
 filetype plugin indent on 
 #''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
 let g:ycm_global_ycm_extra_conf = '~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py'
 set runtimepath+=~/.vim_runtime
 source ~/.vim_runtime/vimrcs/basic.vim
source ~/.vim_runtime/vimrcs/filetypes.vim
 source ~/.vim_runtime/vimrcs/plugins_config.vim
 source ~/.vim_runtime/vimrcs/extended.vim
 
 try
 source ~/.vim_runtime/my_configs.vim
 catch
 endtry
 
 nnoremap <F2> :set invpaste paste?<CR>
 set pastetoggle=<F2>
 set showmode
 
 # auto make pair
......
```

### 直接下载: [.vimrc](https://github.com/bblu/blog.linux/blob/master/vim/.vimrc)
