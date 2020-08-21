---
layout: single
title: "Update Nvidia driver under Ubuntu"
published: true
---
## The most straight forward approach is to install cuda
If there is no reason to have a specific version of Nvidia driver, the easiest way to get it installed would be through CUDA debian package, where a shelved driver has already been included.
Following instructions to install cuda are from <http://developer.nvidia.com>:
```console
$wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
$sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
$wget https://developer.download.nvidia.com/compute/cuda/11.0.3/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.3-450.51.06-1_amd64.deb
$sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.3-450.51.06-1_amd64.deb
$sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
$sudo apt-get update
$sudo apt-get -y install cuda
```
After a reboot, the new driver that has been installed together with cuda will be loaded. Anyway cuda is not something redundant and dozens of applications could get accelerated accordingly.
## Manually update through command line
The latest drivers are always available in <https://www.geforce.com/drivers>, find the appropriate one and download it (those are usually in the form of .run file).

Before the new driver is installed, it is strongly recommended to uninstall the old driver if there is any:
```console
$ sudo apt remove --purge nvidia-*
$ sudo apt autoremove
```
Now switch to console mode before the installation:
```console
$ sudo systemctl set-default multi-user.target
$ sudo reboot
```
Login after reboot:
```console
$ chmod +x ./NVIDIA-Linux-x86_64-xxx.xx.run
$ sudo ././NVIDIA-Linux-x86_64-xxx.xx.run
```
After the driver is successfully installed, let's switch back to the GUI mode:
```console
$ sudo systemctl set-default graphical.target
$ sudo reboot
```
