## Configure remote coding in vscode
[VS code remote tunnel](https://code.visualstudio.com/docs/remote/tunnels) is tool to develop remotely on linux box.

```ssh
curl -Lk 'https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64' --output vscode_cli.tar.gz
tar -xf vscode_cli.tar.gz
```

Then updates the bash_profile

```shell
GOPATH=~/go
PATH=$PATH:$GOPATH/bin
```
Extract and run the vscode tool
`.code/tunnel`

## Install latest go. 
It seems latest podman requires at least go 1.19. However apt-get can only get 1.18. So follow these steps to install latest go [https://go.dev/doc/install](https://go.dev/doc/install)

## Build local development environment

github.com/containers/podman is just the client to interactive with OCI container runtime. We need to build the container runtime to test with podman. The following steps are extracted from [https://podman.io/docs/installation#installing-on-linux](https://podman.io/docs/installation#installing-on-linux)

The steps shown below is for developing in ubuntu.

### Install required packages and enable user namespace
```shell
sudo add-apt-repository universe //some of the required package in in universe collection

sudo apt-get install \
  btrfs-progs \
  crun \
  runc \
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
  pkg-config \
  uidmap \
  slirp4netns

sudo apt-get install containernetworking-plugins
sudo sysctl kernel.unprivileged_userns_clone=1  ## enables user namespace
echo 'kernel.unprivileged_userns_clone=1' > /etc/sysctl.d/userns.conf ## enable it permanently

```

### Install latest conmon
```shell
git clone https://github.com/containers/conmon
cd conmon
export GOCACHE="$(mktemp -d)"
make
sudo make podman
```

### Add registris
```shell
sudo mkdir -p /etc/containers
sudo curl -L -o /etc/containers/registries.conf https://src.fedoraproject.org/rpms/containers-common/raw/main/f/registries.conf
sudo curl -L -o /etc/containers/policy.json https://src.fedoraproject.org/rpms/containers-common/raw/main/f/default-policy.json
```

### Build podman client

```shell
git clone git@github.com:<you>/podman $GOPATH/src/github.com/containers/podman
cd podman
make
```

### Test
```shell
podman/bin/podman run --rm -it busybox /bin/sh
```
