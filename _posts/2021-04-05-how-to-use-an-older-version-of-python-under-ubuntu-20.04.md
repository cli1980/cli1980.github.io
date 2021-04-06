---
layout: single
title: "how to use an older version of python under Ubuntu 20.04"
categories: Note
tags:
  - Ubuntu
  - python
published: true
---
Ubuntu 20.04 comes with an official python package of version 3.8.5. But we always want to keep our projects work after [doing an upgrade](https://ubuntu.com/blog/how-to-upgrade-from-ubuntu-18-04-lts-to-20-04-lts-today) and some of them could be tied to older python versions such as 3.7.x or even 3.6.x. Unfortunately Ubuntu doesn't encourage users to roll back to older versions of any package through apt and we have to try some other approaches.

## Build from the Source
One of them is to build and install the specific version of python from source and method actually is much simpler than it sounds if default configuration is accepted. [Detailed guide](https://devguide.python.org/setup/) is provided by the official site which can be simplified further as following steps:
```console
$ ./configure
$ make -j8
$ sudo make install
```
Lower number helps if there is less than 8 cores in the build system.

The downside is:
1. There could be some minor difference in the installation path from the official Ubuntu package, if the project is tied to some system path (not typical) tweaks are required.
2. Uninstall must be done manually, say, through the infamous 'rm -rf *'

## Gather the Pieces from Ubuntu Package Server
Even though those packages are no longer maintained by the latest release we still can find them one by one from the ubuntu website.

Let's take Python 3.6.9 as an example, following packages can be downloaded from https://packages.ubuntu.com/bionic-updates/python/ and installed in the dependency order:
- [libpython3.6-minimal](https://packages.ubuntu.com/bionic-updates/libpython3.6-minimal)
- [libpython3.6-stdlib](https://packages.ubuntu.com/bionic-updates/python/libpython3.6-stdlib)
- [libpython3.6-dev](https://packages.ubuntu.com/bionic-updates/libpython3.6-dev)
- [python3.6-minimal](https://packages.ubuntu.com/bionic-updates/python/python3.6-minimal)
- [python3.6](https://packages.ubuntu.com/bionic-updates/python/python3.6)
- [python-pip-whl](https://packages.ubuntu.com/bionic-updates/python/python-pip-whl)
- [python-pip](https://packages.ubuntu.com/bionic-updates/python/python-pip)
Installation command using dpkg is as follows:
```console
$ sudo dpkg -i ${PACKAGE}.deb
```

## Switch between the different versions
We can manage different python packages by installing alternatives for one named package under Ubuntu.
```
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 3
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 3
```
Then we can switch between them using 'sudo update-alternatives --config python3'