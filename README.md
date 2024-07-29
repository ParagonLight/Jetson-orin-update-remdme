# Jetson-orin-update-remdme

## Preparation:
    Prepare 

## Update JetPack 6.0 via NVIDIA SDK Manager

The NVIDIA SDK Manager installation and the update for JetPack 6.0 can be found here: [Nvidia Docs](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html).

Notes: some components may occur error when doing installation, check the error information in terminal tab. For example, I was getting error: Nvidia Container Runtime is not installed successfully. It is because the connection problem. I install it manually in Jetson Orin with the command:

```sh
sudo apt-get install nvidia-container
```

## Install and update JetPack Components

Follow the instructions here: [JetPack Components](https://developer.nvidia.com/embedded/learn/get-started-jetson-agx-orin-devkit)


```sh
sudo apt update
sudo apt dist-upgrade
sudo reboot
sudo apt install nvidia-jetpack
```

## Install PyTorch for Jetson

Note: You cannot install PyTorch directly from the PyTorch website or the naive installation tutorials, since the Jetson requires a specific version of PyTorch.

1. Install system packages required by PyTorch:

```sh
sudo apt-get -y update
sudo apt-get -y install python3-pip libopenblas-dev
```

2. Download the wheel file from the [link](https://developer.download.nvidia.cn/compute/redist/jp/v60/pytorch/).

    For example

```sh
wget https://developer.download.nvidia.cn/compute/redist/jp/v60/pytorch/torch-2.4.0a0+3bcc3cddb5.nv24.07.16234504-cp310-cp310-linux_aarch64.whl
```

3. Install PyTorch from the downloaded wheel file:

```sh
python3 -m pip install --upgrade pip
python3 -m pip install numpy
python3 -m pip install --no-cache torch-2.4.0a0+3bcc3cddb5.nv24.07.16234504-cp310-cp310-linux_aarch64.whl
```

If you cannot import torch facing the error: "libcusparseLt.so.0: cannot open shared object file: No such file or directory" try to install cuSPARSELt following the instructions:

```sh
wget https://developer.download.nvidia.com/compute/cusparselt/0.6.2/local_installers/cusparselt-local-repo-ubuntu2204-0.6.2_1.0-1_amd64.deb
sudo dpkg -i cusparselt-local-repo-ubuntu2204-0.6.2_1.0-1_amd64.deb
sudo cp /var/cusparselt-local-repo-ubuntu2204-0.6.2/cusparselt-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install libcusparselt0 libcusparselt-dev
```

The cuSPARSELt installation page can be found here: [Nvidia Docs](https://developer.nvidia.com/cusparselt-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_local).


# Update nvme SSD as a home disk

0. Install SSD to Jetson.

1. Partition your SSD
```sh
sudo fdisk /dev/nvme0n1
```

2. Format your SSD
```sh
sudo mkfs.ext4 /dev/nvme0n1p1
```

3. Mount your SSD, and copy your home directory to the SSD disk
```sh
sudo mkdir -p /mnt/home
sudo mount /dev/nvme0n1p1 /mnt/home
sudo cp -arf /home/* /mnt/home
```

4. Unmount your SSD
```sh
sudo rm -rf /home/*
sudo umount /dev/nvme0n1p1
```

5. uodate ```/etc/fstab```, add ```/dev/nvme0n1p1 /home ext4 defaults 0 0``` to ```/etc/fstab```
```sh
sudo vim /etc/fstab
# add following line to the end of ```/etc/fstab```
/dev/nvme0n1p1 /home ext4 defaults 0 0
```


