# Setting up a virtual machine instance for deep learning

## Creating and configuration of a VM instance with GPU on Google Cloud

*Since my Google Cloud interface is in German, some setting descriptions given in this section are in German (I believe figuring out their equivalent in another language should be easy).*

* A free trial account has 0 GPU quota by default. To request a quota increase follow <https://cloud.google.com/compute/quotas>.

* <https://cloud.google.com> -> Console -> Menu -> Compute Engine -> Create an instance:

* Example config ($1.195 per hour in April 2018):

  - us-east1-b
  - 4 vCPU, 26 GB mem, 1 NVIDIA Tesla P100 GPU (need to click "Anpassen" to get to the GPU settings)
  - Select Linux distribution: Ubuntu 16.04 LTS with SSD-Speicher 30 GB
  - check "Standardzugriff zulassen", "HTTP-Traffic zulassen", "HTTPS-Traffic sulassen", skip the SSH settings
  - under "Laufwerke" *uncheck* "Bootlaufwerk löschen, wenn Instanz gelöscht wird"

### Networking settings

#### Get a static IP

* Menu -> "VPC-Netzwerke" -> "Externe IP Adressen"
  - under "Typ" change "Voruebergehend" to "Statisch"

#### Change the Firewall settings

* This is required to connect with Jupyter Notebook.
* Menu -> "VPC-Netzwerke" -> "Firewallregeln" -> "Firewallregeln erstellen"
  - "Ziele": "Alle Instanzen im Netzwerk"
  - "Quell-IP-Bereiche": "0.0.0.0/0"
  - "Protokolle und Ports": "tcp:5000"

### Connect with the instance

* Install Google Cloud SDK (<https://cloud.google.com/sdk/downloads>)
* ssh into the instance:

```
gcloud compute ssh INSTANCE_NAME
```

__!!! Always remember to shutdown the instance after use !!!__

## Installing CUDA, cuDNN and TensorFlow

Following the instructions for an installation on Ubuntu 16.04 available at <https://www.tensorflow.org/install/install_linux>. *Carefully check the requirements stated there*.

### Installing CUDA

* Install CUDA 9.0
* Code adapted from <https://cloud.google.com/compute/docs/gpus/add-gpus#install-gpu-driver>:

```
#!/bin/bash
echo "Checking for CUDA and installing."
# Check for CUDA and try to install.
if ! dpkg-query -W cuda-9-0; then
  # The 16.04 installer works with 16.10.
  curl -O https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
  dpkg -i ./cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
  apt-get update
  apt-get install cuda-9-0 -y
fi
# Enable persistence mode
nvidia-smi -pm 1
```

* Verify that GPU driver installed

```
nvidia-smi
```

* Set the environment variables for CUDA:

```
echo 'export CUDA_HOME=/usr/local/cuda' >> ~/.bashrc
echo 'export PATH=$PATH:$CUDA_HOME/bin' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$CUDA_HOME/lib64' >> ~/.bashrc
source ~/.bashrc
```

### Installing cuDNN

* Download cuDNN 7.0 for CUDA 9.0 from <https://developer.nvidia.com/cudnn> (need to register an account with nvidia first); April 2018: downloaded version `cudnn-9.0-linux-x64-v7.tgz`
* Transfer the downloaded file to the Google Cloud VM:

```
gcloud compute scp ~/Downloads/cudnn-9.0-linux-x64-v7.tgz deep-learning:~/
```

* Install cuDNN on the instance (see <https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html>):

```
tar xzvf cudnn-9.0-linux-x64-v7.tgz

sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*

rm -rf ~/cuda
rm cudnn-9.0-linux-x64-v7.tgz
```

### Installing TensorFlow

Installation with Virtualenv on Python 3 (based on <https://www.tensorflow.org/install/install_linux>):

```
sudo apt-get install python3-pip python3-dev python-virtualenv
```

* Create a Virtualenv:

```
mkdir ~/tensorflow
virtualenv --system-site-packages -p python3 ~/tensorflow
```

* Activate the Virtualenv:

```
source ~/tensorflow/bin/activate
```

* Install TensorFlow in the active Virtualenv:

```
easy_install -U pip
pip3 install --upgrade tensorflow-gpu # for Python 3.n and GPU
```

* To get rid of some warnings, I had to run for `six` and for a couple other packages:

```
pip3 install --upgrade six
```

#### Testing the TensorFlow-GPU setup

* TensorFlow hello world:

```python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

* From [Using a GPU & TensorFlow on Google Cloud Platform](https://medium.com/google-cloud/using-a-gpu-tensorflow-on-google-cloud-platform-1a2458f42b0):

> Now to test if it was all successful you can use the python code below. It assigns two variables and one operation to the cpu and another two variables and an operation to the GPU. When starting the session we are telling it via the ConfigProto to log the placement of the variables/operations and you should see it printing out on the command line where they are placed.

```python
import tensorflow as tf

with tf.device('/cpu:0'):
    a_c = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a-cpu')
    b_c = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b-cpu')
    c_c = tf.matmul(a_c, b_c, name='c-cpu')


with tf.device('/gpu:0'):
    a_g = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a-gpu')
    b_g = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b-gpu')
    c_g = tf.matmul(a_g, b_g, name='c-gpu')


with tf.Session(config=tf.ConfigProto(log_device_placement=True)) as sess:
    print(sess.run(c_c))
    print(sess.run(c_g))


print('DONE!')
```

* Deactivate Virtualenv (when done using TensorFlow)

```
deactivate
```

## Jupyter Notebook

### Installing Jupyter Notebook

* Within the same `tensorflow` Virtualenv:

```
pip3 install --upgrade pip
pip3 install jupyter
```

### Configure Jupyter Notebook

* Generate a config file, if one does not exist already at `~/.jupyter/jupyter_notebook_config.py`:

```
jupyter notebook --generate-config
```

* Add the following lines into the config file:

```python
c = get_config()
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 5000 # the port number specified in google cloud VM
```

### Run Jupyter Notebook

```
jupyter notebook --no-browser --port=5000
```

Then open in the browser with:

```
http://<External Static IP Address>:<Port Number>
```

## Installing other packages

```
pip3 install keras
pip3 install matplotlib
```

etc.

### Testing Keras

E.g., run [this Jupyter Notebook](./Keras/MNIST_dense.ipynb).

## References

In all cases double check the information with the official docs, because it gets outdated quickly:

* [Set up Google Cloud GPU for fast.ai for v1 and v2](https://medium.com/google-cloud/set-up-google-cloud-gpu-for-fast-ai-45a77fa0cb48) - was not directly helpful, but contains great links within.
* [Running Jupyter Notebook on Google Cloud Platform in 15 min](https://towardsdatascience.com/running-jupyter-notebook-in-google-cloud-platform-in-15-min-61e16da34d52) - Jupyter Notebook setup.
* [Настраиваем VM Instance Google Cloud для задач машинного обучения](https://habrahabr.ru/post/341446/) - decent short guide, but somewhat incomplete.
* [Using a GPU & TensorFlow on Google Cloud Platform](https://medium.com/google-cloud/using-a-gpu-tensorflow-on-google-cloud-platform-1a2458f42b0) - a great but slightly outdated summary of the CUDA, cuDNN, and TensorFlow setup.
