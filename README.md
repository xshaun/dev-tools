# Development Tools

- coding (editor)
  - [VIM](#vim)
  - [SCREEN](#screen)
- coding (terminal)
  - [PuTTY](#putty)
  - [ZSH & oh-my-zsh](#zsh)
- network (VPN)
  - [VPN: IPsec on Azure](#ipsec-on-azure)

---

#### SCREEN

+ **Linux**: copy `screenrc` into `~/.screenrc`

---

#### VIM
Use [The Ultimate vimrc](https://github.com/amix/vimrc)

    # install
    git clone https://github.com/amix/vimrc.git ~/.vim_runtime
    sh ~/.vim_runtime/install_awesome_vimrc.sh

    # update to latest version
    cd ~/.vim_runtime
    git pull --rebase

    # update plugins
    python ~/.vim_runtime/update_plugins.py

But According to my habit, manually add following lines into `~/.vim_runtime/my_configs.vim`

    set number
    set cursorline

##### Issues and Solutions

Have issues about `Unknown function: pathogen#infect` and `Unknown function: pathogen#helptags`, we can add following lines into `~/.vim_runtime/vimrcs/plugins_config.vim`

    """"""""""""""""""""""""""""""
    " => Load pathogen paths
    """"""""""""""""""""""""""""""
    set nocp
    source ~/.vim_runtime/autoload/pathogen.vim

---

#### ZSH

    apt-get install zsh git curl
    chsh -s $(which zsh)
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    # reset ZSH_THEME="ys" in ~/.zshrc

---

#### PuTTY

##### Color Scheme

+ **Windows**: double click `igvita-desert.reg`
+ **Linux**: copy `igvita-desert.ubuntu` into `~/.putty/sessions/`

---


#### IPsec on Azure
details on [./ipsec-on-azure.md](./ipsec-on-azure.md)
