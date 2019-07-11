# Ubuntu new install

## Essential software

### Basics

```
sudo apt update
sudo apt install curl
sudo apt install curl \
  git \
  software-properties-common \
  python-dev python-pip python3-dev python3-pip \
  libcurl4-openssl-dev \
  libssl-dev
```

### Python

#### Anaconda

<https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart>


```
curl -O https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh

sha256sum Anaconda3-2019.03-Linux-x86_64.sh
45c851b7497cc14d5ca060064394569f724b67d9b5f98a926ed49b834a6bb73a  Anaconda3-2019.03-Linux-x86_64.sh

source ~/.bashrc

conda list
```

### R

<https://cran.r-project.org/bin/linux/ubuntu/README.html>

1. Add an appropriate entry to your `/etc/apt/sources.list` file; possible, also activate the Ubuntu "backports" repositories.
2. Add key to your system. There are multiple options. See "Secure APT" at the link above.
3. `sudo apt install r-base r-base-dev`.

### NeoVim

1. Install

	```
	sudo add-apt-repository ppa:neovim-ppa/stable
	sudo apt update
	sudo apt install neovim
	```

2. `ln -s /path/to/dotfiles/backup/init.vim /home/alexej/.config/init.vim`
3. Install vim-plug (check <https://github.com/junegunn/vim-plug> for details):

	```
	curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    		https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
	```

4. Install plugins with `:PlugInstall`. Install additional software as needed for all plugins to work (see in the `init.vim` file).

### Tmux

1. `sudo apt install tmux`
2. `mkdir ~/.tmux`
3. `ln -s` files from `dotfiles/tmux` into `~/.tmux`
4. Link, or copy/edit `~/.tmux.conf`
5. Install Tmux Plugin Manager: <https://github.com/tmux-plugins/tpm>. Then use it to install plugins specified in `.tmux.conf`.

### LaTeX

```
sudo apt install texlive-full texmaker
```

### Docker

Install Docker

### Keepass2

Use Docker file
