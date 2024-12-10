---
title: Install podman 5.0.4 on Linux Mint 21.3   
subtitle:  Install podman on Linux Mint 21.3 from source
date: 2024-12-10
tags: ["podman", "linux mint", "21.3", "podman source", "podman 5"]
---

# Build podman from source on linux mint 21.3

Podman on linux mint 21.3 is relative old. This page describes the Installation of podman 5.4.0-dev on a linux mint 21.3 system.

In the repository of linux mint 21.3 you will find an old version: 

```
apt-cache madison podman
    podman | 3.4.4+ds1-1ubuntu1.22.04.3 | http://ftp.hosteurope.de/mirror/archive.ubuntu.com jammy-updates/universe amd64 Packages
    podman | 3.4.4+ds1-1ubuntu1.22.04.3 | http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages
    podman | 3.4.4+ds1-1ubuntu1 | http://ftp.hosteurope.de/mirror/archive.ubuntu.com jammy/universe amd64 Packages
```

The good installation guide to build from source is described on https://podman.io/docs/installation#building-from-source. Unfortunately, the documentation is not up to date. Podman 5 requires netavark and aardvark-dns. The solution that the containernetworking-plugins package could be installed as an alternative unfortunately did not work (see https://github.com/containers/podman/discussions/24111).

## Prepare

The following packages need to be installed:

```
sudo apt-get install \
  btrfs-progs \
  git \
  golang-go \
  go-md2man \
  iptables \
  libassuan-dev \
  libbtrfs-dev \
  libc6-dev \
  libdevmapper-dev \
  libglib2.0-dev \
  libgpgme-dev \
  libgpg-error-dev \
  libprotobuf-dev \
  libprotobuf-c-dev \
  libseccomp-dev \
  libselinux1-dev \
  libsystemd-dev \
  make \
  netavark \
  pkg-config \
  uidmap \
  libapparmor-dev \
  libgpgme-dev \
  libseccomp-dev \
  libbtrfs-dev \
  runc
```

Enable user namespace if it is not already enabled.

`sudo sysctl kernel.unprivileged_userns_clone=1`

and enable user namespace permanently

`echo 'kernel.unprivileged_userns_clone=1' > /etc/sysctl.d/userns.conf`


## Install golang

Install the latest golang, version 1.16.x is not high enough:

as user root

```
mkdir ~/podman
cd ~/podman
wget https://dl.google.com/go/go1.23.4.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.23.4.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
go version
```

## Install conmon

The latest version of conmon is expected to be installed on the system. Conmon is used to monitor OCI Runtimes. To build from source, use the following:

Run as user root

```
apt install libglib2.0-dev
pkg-config --modversion glib-2.0
cd ~/podman
git clone https://github.com/containers/conmon
cd conmon
export GOCACHE="$(mktemp -d)"
make
sudo make podman
```

## Check runc

In the original documentation crun or runc should be installed. Unfortunatelly podman has some trouble with crun, I have deinstalled crun and only installed runc.

`runc --version -> must be >= 1.0.1`

## Download Configuration Templates

```
sudo mkdir -p /etc/containers
sudo curl -L -o /etc/containers/registries.conf https://raw.githubusercontent.com/containers/image/main/registries.conf
sudo curl -L -o /etc/containers/policy.json https://raw.githubusercontent.com/containers/image/main/default-policy.json
```

## Download and Build podman

With BUILDTAGS you could enable/disable some features from podman. Please check the table which features are possible (https://podman.io/docs/installation#build-tags)

Run as user root

```
cd ~/podman
git clone https://github.com/containers/podman/
cd podman
make BUILDTAGS="selinux seccomp" PREFIX=/usr
make install PREFIX=/usr
```


## Install netavark

Netavark is now mandatory. The linux mint repository has no package for version 21.3. So you have to install rust and some other stuff to get the software compiled.


###  Install Rust
   
`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

### Install protoc

`apt-get install protobuf-compiler`

### Install go-md2man:
    
`apt install go-md2man`

### Build netavark

```
cd ~/podman/
git clone https://github.com/containers/netavark.git
cd netavark
make
make docs
make install
```

## Install aardvark-dns (Support for container DNS resolution)

Aardvark-dns is used by netavark and need also to be compiled. As user root run ...

```
cd ~/podman
git clone https://github.com/containers/aardvark-dns.git
cd aardvark-dns
make 
make install
```

## Install pasta 

(https://passt.top/)

```
cd ~/podman
git clone https://passt.top/passt
cd passt
make
make install
```

Now all dependencies are created and installed and you could test podman.

## Test Podman

Podman version:

as user run ...

```
podman version
Client:       Podman Engine
Version:      5.4.0-dev
API Version:  5.4.0-dev
Go Version:   go1.23.4
Git Commit:   07dddebd1209ec1cabc35613d970fc821618fd2c
Built:        Mon Dec  9 21:26:52 2024
OS/Arch:      linux/amd64
```

Test NGINX Container:

as user run ...

```
podman pull nginx:latest
podman run -d --name my_nginx -p 8080:80 nginx:latest
podman ps
curl localhost:8080
podman stop my_nginx
podman rm my_nginx
```