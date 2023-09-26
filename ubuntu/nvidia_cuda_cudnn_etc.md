# NVIDIA Drivers, CUDA, CUDA Toolkit, and cuDNN installation on Ubuntu 18.04 or 20.04

## 1. Old CUDA installations

Likely this step can be skipped, but in some cases it may be necessary to remove old CUDA installations according to the instructions given at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#handle-conflicting-installation-methods>. It may also be needed to remove the old cuda installation sources from apt.

## 2. Install NVIDIA drivers, CUDA, and CUDA Toolkit

- Follow the installation instructions at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#ubuntu>.
- The required installation files should be downloaded from <https://developer.nvidia.com/cuda-downloads>, where you select the OS, architecture, and distribution, to get the link to the appropriate version.
- For example, for Ubuntu 20.04 "local install" use <https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local>
- This online tool also provides specialized installation instructions for your choices.
- For example, the provided instructions to install CUDA 12 on Ubuntu 20.04 x86_64:

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.2.2/local_installers/cuda-repo-ubuntu2004-12-2-local_12.2.2-535.104.05-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-12-2-local_12.2.2-535.104.05-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2004-12-2-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
```

### 2.1 Post-installation

- The detailed post-installation instructions are found at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions>
- In most cases the following will suffice:
    1. Add the following line to the end of the `~/.bashrc` file:
    ```
    export PATH=/usr/local/cuda-12.2/bin${PATH:+:${PATH}}
    ```
    2. Remove any other CUDA related lines from `~/.bashrc`, for example, those left over from previous installations of CUDA 10, 11, etc. These could be changes to `PATH` or `LD_LIBRARY_PATH`.

### 3. Verify installation of NVIDIA drivers, CUDA, and CUDA Toolkit

- Driver: check the output of `cat /proc/driver/nvidia/version`
- CUDA: check `nvcc -V` or `cat /usr/local/cuda/version.json`
- CUDA Toolkit: see the output of `dpkg -l | grep cuda-toolkit`

### 4. Install CUDNN

- Easiest way seems to be: <https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#package-manager-ubuntu-install>
- Alternative way is to download "cuDNN Library for Linux (x86_64)": <https://developer.nvidia.com/rdp/form/cudnn-download-survey>. And then following the procedure at <https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#installlinux-tar>.

## Other resources

- Some helpful info (but some outdated) here: <https://medium.com/analytics-vidhya/installing-tensorflow-with-cuda-cudnn-gpu-support-on-ubuntu-20-04-f6f67745750a>
