# Development Weapons
PuTTY &amp; VIM &amp; SCREEN

## PuTTY
#### [How to copy & paste](http://xshaun.github.io/windows%E5%B0%8F%E8%A7%81/2017/04/10/putty%E5%A4%8D%E5%88%B6%E7%B2%98%E8%B4%B4)

#### Color Scheme
*Windows*   

    double click `igvita-desert.reg`    

*Linux*  

    install putty
    copy `igvita-desert.ubuntu` into `~/.putty/sessions/`

## VIM
Use [The Ultimate vimrc](https://github.com/amix/vimrc)

    # install
    git clone https://github.com/amix/vimrc.git ~/.vim_runtime
    sh ~/.vim_runtime/install_awesome_vimrc.sh

    # update to latest version
    cd ~/.vim_runtime
    git pull --rebase

    # update plugins
    python ~/.vim_runtime/update_plugins.py

#### Issues and Solutions  

Have issues about `Unknown function: pathogen#infect` and `Unknown function: pathogen#helptags`, we can add following lines into `~/.vim_runtime/vimrcs/plugins_config.vim`

    """"""""""""""""""""""""""""""
    " => Load pathogen paths
    """"""""""""""""""""""""""""""
    set nocp
    source ~/.vim_runtime/autoload/pathogen.vim

## SCREEN

#### [How to use](http://xshaun.github.io/linux%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/2017/04/10/Screen%E5%91%BD%E4%BB%A4)

*Linux*  

    cp `screenrc` into `~/.screenrc`
