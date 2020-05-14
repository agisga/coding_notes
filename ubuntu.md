# Alexej's post-Ubuntu-installation setup

Last tested with Ubuntu 20.04.

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
  libxml2-dev
```

### Some useful software applications from the Ubuntu repos

```
sudo apt install chromium-browser
sudo apt install gnome-tweak-tool
```

### Connect to the machine via SSH on the same network

```
sudo apt install openssh-client openssh-server
```

Then find the IP using for example `ifconfig`.

### NVIDIA GPU drivers, CUDA Toolkit, cuDNN

Based on <https://illya13.github.io/RL/tutorial/2020/04/28/installing-pytorch-on-ubuntu-20.html>.
This installs CUDA Toolkit and cuDNN manually. Alternatively, an installation with Anaconda may be much easier (it seems that Anaconda has packaged CUDA Toolkit as well as cuDNN, but I haven't tested it).

CUDA 10.2 requires GCC <= 8. So the first thing we need to do is to install compatible versions of `gcc-8`. But let's use `gcc-9` for now (e.g., it should be used to install the GPU Drivers).

```
sudo apt install gcc-8 g++-8 gcc-9 g++-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 8 --slave /usr/bin/g++ g++ /usr/bin/g++-8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9 --slave /usr/bin/g++ g++ /usr/bin/g++-9
sudo update-alternatives --config gcc
# choose gcc-9
```

#### NVIDIA GPU drivers

Partially based on <https://medium.com/@redowan/no-bullshit-guide-on-installing-tensorflow-gpu-ubuntu-18-04-18-10-238924cc4a6a>.

- We need to replace the GPU driver provided with Ubuntu 20.04 by the newest available version.
- Download the current drivers from <https://www.geforce.com/drivers>.
- Stop desktop manager (need to run it after reboots below too):
  ```
  sudo telinit 3
  ```
- Remove any other NVIDIA drivers:
  ```
  # probably either one of these two should be enough...
  sudo dpkg -P $(dpkg -l | grep nvidia-driver | awk '{print $2}')
  sudo apt-get purge nvidia*

  sudo apt autoremove
  sudo reboot
  ```
- Remove/blacklist the Nouveau drivers:
  ```
  sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
  sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
  ```
- Confirm that Nouveau is blacklisted:
  ```
  > cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf

  blacklist nouveau
  options nouveau modeset=0
  ```
- Update kernel initramfs and reboot:
  ```
  sudo update-initramfs -u
  sudo reboot
  ```
- Install and enable the new driver:
  ```
  sudo bash NVIDIA-Linux-x86_64-440.82.run
  ```
- At the end you should get: `Installation of the NVIDIA Accelerated Graphics Driver for Linux-x86_64 (version: 440.82) is now complete.`
- Reboot, and verify installation with `nvidia-smi`.

#### CUDA Toolkit

- Download CUDA Toolkit 10.2 **runfile** from <https://developer.nvidia.com/cuda-toolkit>.
- Switch `gcc` to `gcc-8`.
  ```
  sudo update-alternatives --config gcc
  # choose gcc-8
  ```
- Run installer, accept license, deselect Driver
  ```
  > sudo bash cuda_10.2.89_440.33.01_linux.run

  x CUDA Installer                                                               x
  x - [ ] Driver                                                                 x
  x      [ ] 440.33.01                                                           x
  x + [X] CUDA Toolkit 10.2                                                      x
  x   [X] CUDA Samples 10.2                                                      x
  x   [X] CUDA Demo Suite 10.2                                                   x
  x   [X] CUDA Documentation 10.2                                                x
  x   Options                                                                    x
  x   Install                                                                    x
  ```
- After successful install update `PATH` and `LD_LIBRARY_PATH` as suggested by the final install message.

#### cuDNN

- Download cuDNN for CUDA 10.2 <https://developer.nvidia.com/cudnn> in `.tgz` format (not the `.deb` package).
- From <https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html>:
  ```
  tar -xzvf cudnn-10.2-linux-x64-v7.6.5.32.tgz
  sudo cp cuda/include/cudnn.h /usr/local/cuda/include
  sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
  sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
  ```
- Additional steps from <https://illya13.github.io/RL/tutorial/2020/04/28/installing-pytorch-on-ubuntu-20.html>:
  ```
  rm /usr/local/cuda-10.2/targets/x86_64-linux/lib/libcudnn.so.7 /usr/local/cuda-10.2/targets/x86_64-linux/lib/libcudnn.so
  sudo ln -s libcudnn.so.7.6.5 /usr/local/cuda-10.2/targets/x86_64-linux/lib/libcudnn.so.7
  sudo ln -s libcudnn.so.7 /usr/local/cuda-10.2/targets/x86_64-linux/lib/libcudnn.so
  sudo ldconfig
  ```

### Python

I will go with the system `python3` and the regular `venv` for this configuration.
```
sudo apt install python3-pip python3-venv
```

Not sure if any of these are actually needed (list copied from <https://illya13.github.io/RL/tutorial/2020/04/28/installing-pytorch-on-ubuntu-20.html>), but they may be required for PyTorch and related Python packages below:
```
sudo apt install zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev libffi-dev
```

#### PyTorch

After the above preparatory steps for GPU support, we can install PyTorch.
This is a template/example of how to create a project-specific virtual environment install PyTorch in it.
As an example, we will benchmark the GPU using the code from <https://github.com/ryujaehun/pytorch-gpu-benchmark> (we can compare the results with their numbers).

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

<https://cran.r-project.org/bin/linux/ubuntu/README.html>

1. Add an appropriate entry to your `/etc/apt/sources.list` file; possible, also activate the Ubuntu "backports" repositories.
2. Add key to your system. There are multiple options. See "Secure APT" at the link above.
3. `sudo apt install r-base r-base-dev`.
4. Install RStudio: <https://www.rstudio.com/products/rstudio/download/>.
5. Install packages (`tidyverse`, `caret`, etc.).

### NeoVim

1. Install

  ```
  sudo apt update
  sudo apt install neovim
  ```

2. `ln -s /path/to/dotfiles/backup/init.vim /home/alexej/.config/nvim/init.vim`
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
