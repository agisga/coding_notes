# Alexej's post-Ubuntu-installation setup

## Essential software

### Some development tools from the Ubuntu repos

```
sudo apt update
sudo apt install \
  curl \
  git \
  software-properties-common \
  libcurl4-openssl-dev \
  libssl-dev \
  apt-transport-https \
  ca-certificates \
  gnupg-agent \
```

### Some useful software applications form the Ubuntu repos

```
sudo apt install chromium-browser
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
4. Install RStudio: <https://www.rstudio.com/products/rstudio/download/>.
5. Install packages (`tidyverse`, `caret`, etc.).

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

#### Installation

<https://docs.docker.com/install/linux/docker-ce/ubuntu/>

```
sudo apt-get remove docker docker-engine docker.io containerd runc
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   test"
```

I use `test` instead of `stable` in the above, because current `docker-ce` is not available in the stable repositories for Ubuntu 19.04.

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

#### Manage Docker as a non-root user

<https://docs.docker.com/install/linux/linux-postinstall/>

```
sudo groupadd docker
sudo usermod -aG docker $USER
```

Log out your user and log back in. Then verify that you can run `docker` without `sudo`.

```
docker run hello-world
```

#### Configure Docker to start on boot

<https://docs.docker.com/install/linux/linux-postinstall/>

```
sudo systemctl enable docker
```

#### Specify DNS servers for Docker

<https://docs.docker.com/install/linux/linux-postinstall/>

* Add the DNS IPs to `/etc/docker/daemon.json` (create or edit), for example:

  ```
  {
    "dns": ["8.8.8.8", "8.8.4.4"]
  }
  ```

* Then restart the Docker service: `sudo service docker restart`
* Check that it works: `docker pull hello-world`

### Keepass2 password safe/manager

I like to run it from within a Docker container as described here: <https://github.com/agisga/dockerfiles/blob/master/keepass2/Dockerfile>
