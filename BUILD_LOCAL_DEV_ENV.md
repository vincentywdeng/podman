## Build local development environment

github.com/containers/podman is just the client to interactive with OCI container runtime. We need to build the container runtime to test with podman. The following steps are extracted from [https://podman.io/docs/installation#installing-on-linux](https://podman.io/docs/installation#installing-on-linux)

The steps shown below is for developing in ubuntu.

### Install required packages and enable user namespace
```ssh
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
sudo sysctl kernel.unprivileged_userns_clone=1  // enables user namespace
echo 'kernel.unprivileged_userns_clone=1' > /etc/sysctl.d/userns.conf // enable it permanently

```

### Install latest conmon
```ssh
git clone https://github.com/containers/conmon
cd conmon
export GOCACHE="$(mktemp -d)"
make
sudo make podman
```

### Add registris
```ssh
sudo mkdir -p /etc/containers
sudo curl -L -o /etc/containers/registries.conf https://src.fedoraproject.org/rpms/containers-common/raw/main/f/registries.conf
sudo curl -L -o /etc/containers/policy.json https://src.fedoraproject.org/rpms/containers-common/raw/main/f/default-policy.json
```

### Build podman

```ssh
cd podman
make
```

### Test
```ssh
podman/bin/podman run --rm -it busybox /bin/sh
```