# Alexej's post-Ubuntu-installation setup

Last tested with Ubuntu 22.04.

## (If needed) Disable all automated power management functions

Copied from <http://www.vassox.com/linux-general/ubuntu/disabling-ubuntu-suspend-power-management-features-from-the-command-line/>:

> In some configurations, Ubuntu power conservation options are enabled by default.  For server machines this is something that can make the machine unavailable and is generally unsuitable for a server machine.  In a headless (non-GUI) server, these functions can be disabled as follows from the command line:

Disabling power management functions:
```
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

Re-Enabling power management functions:
```
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

## Essential software

### Some development tools from the Ubuntu repos

```
sudo apt update
sudo apt install \
  build-essential \
  curl \
  git \
  software-properties-common \
  libcurl4-openssl-dev \
  libssl-dev \
  apt-transport-https \
  ca-certificates \
  gnupg-agent \
  net-tools \
  libxml2-dev \
  tmux \
  htop
```

### Some useful software applications from the Ubuntu repos

```
sudo apt install chromium-browser
sudo apt install gnome-tweaks
```

### Connect to the machine via SSH on the same network

```
sudo apt install openssh-client openssh-server
```

Then find the IP using for example `ifconfig`.

### NVIDIA GPU drivers, CUDA Toolkit, cuDNN

See `./nvidia_cuda_cudnn_etc.md`.

### Python

#### Python installation and versions

Not sure if any of these are actually needed (list copied from <https://illya13.github.io/RL/tutorial/2020/04/28/installing-pytorch-on-ubuntu-20.html>), but they may be required for PyTorch and related Python packages below:
```
sudo apt install zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev libffi-dev
```

##### System Python

A straightforward approach is to use the system `python3` with the regular `venv`.
```
sudo apt install python3-pip python3-venv
```

##### pyenv

One can conveniently switch between different versions of Python on the same computer using `pyenv`. See my notes in [./python/pyenv.md](pyenv.md).

#### PyTorch

After the above preparatory steps for GPU support, we can install PyTorch.
This is a template/example of how to create a project-specific virtual environment install PyTorch in it.
As an example, we will benchmark the GPU using the code from <https://github.com/ryujaehun/pytorch-gpu-benchmark> (we can compare the results with their numbers).
If the benchmarking project above does not work, then just run the script from <https://github.com/BAI-Yeqi/PyTorch-Verification>.

- Clone the benchmarking project, and create a Python virtual environment for this project, and activate it:
  ```
  git clone https://github.com/ryujaehun/pytorch-gpu-benchmark
  cd pytorch-gpu-benchmark/

  python3 -m venv env
  source env/bin/activate
  ```
- Install PyTorch and Torchvision (within the venv):
  ```
  pip install torch torchvision
  ```
- Check GPU support is enabled, and you can access your GPU:
  ```
  > python -c "from __future__ import print_function; import torch; print(torch.cuda.is_available())"
  True
  ```
- Run the benchmarks:
  ```
  pip install psutil plotly cufflinks pandas matplotlib
  python benchmark_models.py
  # monitor nvidia-smi -l 1 in another terminal
  ```
- Deactivate the virtual environment:
  ```
  deactivate
  ```

### R

From "Ubuntu Packages For R - Brief Instructions" at <https://cran.r-project.org>.

```
# update indices
sudo apt update -qq
# install two helper packages we need
sudo apt install --no-install-recommends software-properties-common dirmngr
# add the signing key (by Michael Rutter) for these repos
# To verify key, run gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc 
# Fingerprint: E298A3A825C0D65DFD57CBB651716619E084DAB9
wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
# add the R 4.0 repo from CRAN -- adjust 'focal' to 'groovy' or 'bionic' as needed
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
# install R
sudo apt install --no-install-recommends r-base r-base-dev
```

Next steps:

1. Install RStudio: <https://www.rstudio.com/products/rstudio/download/>.
2. Install packages (`tidyverse`, `caret`, etc.).

### NeoVim

1. Install

  ```
  sudo apt update
  sudo apt install neovim
  ```

2. `ln -s /path/to/dotfiles/backup/init.vim /home/alexej/.config/nvim/init.vim`
3. Install vim-plug. See <https://github.com/junegunn/vim-plug#neovim>.
4. Install plugins with `:PlugInstall`. Install additional software as needed for all plugins to work (see in the `init.vim` file).

### Tmux

1. `sudo apt install tmux`
2. `mkdir ~/.tmux`
3. `ln -s` files from `dotfiles/tmux` into `~/.tmux`
4. Link, or copy/edit `~/.tmux.conf`
5. (If desired) Install Tmux Plugin Manager: <https://github.com/tmux-plugins/tpm>. Then use it to install plugins specified in `.tmux.conf`.

### LaTeX

```
sudo apt install texlive-full texmaker
```

### Docker

#### Installation

The easiest way is to use the `docker.io` package provided by Canonical:

```
sudo apt install docker-compose
```

##### Alternative installation using the package provided by docker.com

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

#### NVIDIA Container Toolkit

Based on <https://illya13.github.io/RL/tutorial/2020/04/28/installing-pytorch-on-ubuntu-20.html>

Add the package repositories and install:
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

Validate the installation:
```
docker run --gpus all --rm nvidia/cuda nvidia-smi
```

Run PyTorch in a docker container:
```
> docker run -it --rm --gpus all pytorch/pytorch python -c "from __future__ import print_function; import torch; print(torch.cuda.is_available())"
True
```

Run the PyTorch benchmark in a docker container (see under PyTorch above):
```
> docker run --gpus all --shm-size=512M -it --rm -v /path/to/pytorch-gpu-benchmark:/pytorch-gpu-benchmark pytorch/pytorch
(in a docker) > pip install psutil cufflinks plotly pandas matplotlib
(in a docker) > python /pytorch-gpu-benchmark/benchmark_models.py
```

### Keepass2 password safe/manager

I like to run it from within a Docker container as described here <https://github.com/agisga/dockerfiles/blob/master/keepass2/Dockerfile>, and here <https://github.com/agisga/dockerfiles/blob/master/passhole/Dockerfile>.

### Use PIN instead of password from GDM lock screen

Following the instructions from <https://randomblog.hu/setting-up-pin-unlock-for-ubuntu-linux/>.
With this you cannot use the PIN code for the first time you try to log in after starting up your computer, and you can't gain root privileges with it. For that you still need your password.

```
sudo apt install -y libpam-pwdfile whois
sudo -i
cp /etc/pam.d/gdm-password /etc/pam.d/gdm-password_ORIGINAL
echo "myusername:$(mkpasswd -5)" > /etc/custompinfile
```

- Then open the file `/etc/pam.d/gdm-password` and add the following line after `%PAM-1.0`:
```
auth    sufficient  pam_pwdfile.so pwdfile=/etc/custompinfile
```


