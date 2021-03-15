# NVIDIA CUDA 11, CUDA Toolkit, and cuDNN installation on Ubuntu 18.04 or 20.04

**Note:** The following assumes that the NVIDIA GPU drivers are already installed and sufficiently up-to-date.

## 1. Old CUDA installations

Likely this step can be skipped, but in some cases it may be necessary to remove old CUDA installations according to the instructions given at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#handle-uninstallation>

## 2. Install CUDA 11

- The detailed documentation is found at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html>
- Below are the steps for Ubuntu 18.04.
- For Ubuntu 20.04 use <https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=2004&target_type=deblocal>
- This online tool also allows for the choice of many different environments and OSs to get specialized instructions.

**To install CUDA 11.2.2 on Ubuntu 18.04 run the following commands in the terminal:**

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin

sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget https://developer.download.nvidia.com/compute/cuda/11.2.2/local_installers/cuda-repo-ubuntu1804-11-2-local_11.2.2-460.32.03-1_amd64.deb

sudo dpkg -i cuda-repo-ubuntu1804-11-2-local_11.2.2-460.32.03-1_amd64.deb

sudo apt-key add /var/cuda-repo-ubuntu1804-11-2-local/7fa2af80.pub

sudo apt-get update

sudo apt-get -y install cuda
```

### 2.1 Post-installation

- The detailed post-installation instructions are found at <https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-- installation-actions>
- In most cases the following will suffice:
    1. Add the following line to the end of the `~/.bashrc` file:
    ```
    export PATH=/usr/local/cuda-11.2/bin${PATH:+:${PATH}}
    ```
    2. Remove any other CUDA related lines from `~/.bashrc`, for example, those left over from previous installations of CUDA 10, etc. These could be changes to `PATH` or `LD_LIBRARY_PATH`.

## 3. Install NVIDIA CUDA Toolkit:

```
sudo apt install nvidia-cuda-toolkit
```

### 4. Verify installation of NVIDIA drivers, CUDA, and CUDA Toolkit

- Driver: check the output of `cat /proc/driver/nvidia/version`
- CUDA: check `nvcc -V` or `cat /usr/local/cuda/version.json`
- CUDA Toolkit: see the output of `dpkg -l | grep cuda-toolkit`

### 5. Install CUDNN

- Download "cuDNN Library for Linux (x86_64)": <https://developer.nvidia.com/rdp/form/cudnn-download-survey>
- Follow the procedure at <https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#installlinux-tar>

## Other resources

- Some helpful info (but some outdated) here: <https://medium.com/analytics-vidhya/installing-tensorflow-with-cuda-cudnn-gpu-support-on-ubuntu-20-04-f6f67745750a>
