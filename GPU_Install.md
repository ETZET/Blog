# GPU Driver Installation

Department: URSeismo Lab
Author: Enting Zhou
Cited from: Nvidia CUDA Toolkit Documentation

This documents record the installation of GPU Driver Nvidia GTX series on RHEL 7.9. For installation of Nvidia drivers on other system, refer to Nvidia Cuda Toolkit Documentation.

### 1. Preinstallation

##### 1.1 Verify that you have a CUDA capable GPU

`lspci | grep -i nvidia`

A sample output look like:
```
65:00.0 VGA compatible controller: NVIDIA Corporation GA102 [GeForce RTX 3090] (rev a1)
```

If you do not see your device, make sure the GPU is properly inserted at the mother board and required PCI cable is connected.

##### 1.2 Install Driver Dependency

The NVIDIA driver requires that the kernel headers and development packages for the running version of the kernel be installed at the time of the driver installation, as well whenever the driver is rebuilt. For example, if your system is running kernel version 4.4.0, the 4.4.0 kernel headers and development packages must also be installed. Install the correct kernel and kernel header by,

`sudo yum install -y kernel-devel-$(uname -r) kernel-headers-$(uname -r)`

Install EPEL to satisfy DKMS dependency,

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`

##### 1.3 Enable optional repos

`sudo subscription-manager repos --enable="rhel-*-optional-rpms" --enable="rhel-*-extras-rpms"  --enable="rhel-ha-for-rhel-*-server-rpms"`

**Important Note: This is dependent on your machine. For our machine, this command introduce a dead repos that upset the yum environement.**

After this step, do a `sudo yum update`, to check whether yum is functioning properly. If you encounter a "[Errno 14] HTTPS Error 404 - Not Found", run the following command to disable the dead repo.

`yum-config-manager --disable rhel-*-optional-rpms` 


### 2. Installation

##### 2.1 Set up CUDA network repository

```bash
distribution=$(. /etc/os-release;echo $ID`rpm -E "%{?rhel}%{?fedora}"`)
ARCH=$( /bin/arch )
sudo yum-config-manager --add-repo http://developer.download.nvidia.com/compute/cuda/repos/$distribution/${ARCH}/cuda-$distribution.repo
```     

##### 2.2 Install Driver and CUDA kit

Install Nvidia Driver and CUDA kit by,

`sudo yum install -y nvidia-driver-latest-dkms cuda cuda-drivers`

**Reboot machine for the installation to take effect**

### 3. Postinstallation

##### 3.1 Environement Setup

Update the PATH variable:

`export PATH=/usr/local/cuda-11.7/bin${PATH:+:${PATH}}`

Update the LIBARY PATH variable:

`export LD_LIBRARY_PATH=/usr/local/cuda-11.7/lib64\
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}`


**Reboot machine for the above change to take effect**

### 4. Check Installation

After reboot, Nvidia GPU should be successfully connect to the system, check the gpu status by command,

`nvidia-smi`

A sample output will be like,

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 515.57       Driver Version: 515.57       CUDA Version: 11.7     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:65:00.0 Off |                  N/A |
|  0%   47C    P8    29W / 370W |      0MiB / 24576MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```