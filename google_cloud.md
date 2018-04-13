# Setting up a virtual machine instance for deep learning

A free trial account has 0 GPU quota by default. To request a quota increase follow <https://cloud.google.com/compute/quotas>.

The below describes the following steps:

1. Creating and configuration of a VM instance on Google Cloud
2. Networking settings
3. Installation of Anaconda, and other packages
4. Configuration of Jupyter Notebook
5. Configuration of File Transfer

## Using the web interface

### Creating and configuration of a VM instance on Google Cloud

<https://cloud.google.com> -> Console -> Menu -> Compute Engine -> Create an instance:

  - us-east1-b
  - 4 vCPU, 26 GB mem, 1 NVIDIA Tesla P100 GPU (April 2018, need to click "Anpassen" to get to the GPU settings): 1.188$ per hour
  - Select Linux distribution: e.g., Debian 9 (stretch)
  - check "Standardzugriff zulassen", "HTTP-Traffic zulassen", "HTTPS-Traffic sulassen", skip the SSH settings
  - under "Laufwerke" *uncheck* "Bootlaufwerk löschen, wenn Instanz gelöscht wird"

### Networking settings

* Menu -> "VPC-Netzwerke"
