# Jetson Orin Update README

## Preparation
Before proceeding, ensure you have the necessary tools and dependencies prepared. 

1. **Ubuntu 22.04 LTS:** You may prepare a virtual machine to install Ubuntu.
2. **Connect Jetson to the Ubuntu 22.04.**.

## Update JetPack 6.0 via NVIDIA SDK Manager

The NVIDIA SDK Manager installation and the update instructions for JetPack 6.0 can be found [here](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html).

**Note:** During installation, some components may encounter errors. For example, you might receive an error stating: "Nvidia Container Runtime is not installed successfully." This could be due to connection issues. To resolve this, manually install the component on Jetson Orin using the following command:

```sh
sudo apt-get install nvidia-container
```

## Install and Update JetPack Components

Follow the instructions provided [here](https://developer.nvidia.com/embedded/learn/get-started-jetson-agx-orin-devkit).

```sh
sudo apt update
sudo apt dist-upgrade
sudo reboot
sudo apt install nvidia-jetpack
```

## Install PyTorch for Jetson

**Note:** Do not install PyTorch directly from the PyTorch website or through standard installation tutorials, as the Jetson requires a specific version of PyTorch.

1. Install the system packages required by PyTorch:

```sh
sudo apt-get -y update
sudo apt-get -y install python3-pip libopenblas-dev
```

2. Download the wheel file from the [NVIDIA link](https://developer.download.nvidia.cn/compute/redist/jp/v60/pytorch/).

    For example:

```sh
wget https://developer.download.nvidia.cn/compute/redist/jp/v60/pytorch/torch-2.4.0a0+3bcc3cddb5.nv24.07.16234504-cp310-cp310-linux_aarch64.whl
```

3. Install PyTorch from the downloaded wheel file:

```sh
python3 -m pip install --upgrade pip
python3 -m pip install numpy
python3 -m pip install --no-cache torch-2.4.0a0+3bcc3cddb5.nv24.07.16234504-cp310-cp310-linux_aarch64.whl
```

If you encounter an error stating: "libcusparseLt.so.0: cannot open shared object file: No such file or directory," install cuSPARSELt by following these instructions:

```sh
wget https://developer.download.nvidia.com/compute/cusparselt/0.6.2/local_installers/cusparselt-local-repo-ubuntu2204-0.6.2_1.0-1_amd64.deb
sudo dpkg -i cusparselt-local-repo-ubuntu2204-0.6.2_1.0-1_amd64.deb
sudo cp /var/cusparselt-local-repo-ubuntu2204-0.6.2/cusparselt-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install libcusparselt0 libcusparselt-dev
```

For more information, visit the [cuSPARSELt installation page](https://developer.nvidia.com/cusparselt-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_local).

## Update NVMe SSD as Home Disk

1. Install the SSD in Jetson.

2. Partition your SSD:

```sh
sudo fdisk /dev/nvme0n1
```

3. Format your SSD:

```sh
sudo mkfs.ext4 /dev/nvme0n1p1
```

4. Mount your SSD and copy your home directory to the SSD:

```sh
sudo mkdir -p /mnt/home
sudo mount /dev/nvme0n1p1 /mnt/home
sudo cp -arf /home/* /mnt/home
```

5. Unmount your SSD:

```sh
sudo rm -rf /home/*
sudo umount /dev/nvme0n1p1
```

6. Update `/etc/fstab`. Add the following line to `/etc/fstab`:

```sh
sudo vim /etc/fstab
# Add the following line to the end of /etc/fstab
/dev/nvme0n1p1 /home ext4 defaults 0 0
```