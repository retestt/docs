Table of Contents
------------------

- [Setting up Fedora for Gitian building](#setting-up-fedora-for-gitian-building)
- [Installing Gitian](#installing-gitian)
- [Setting up the Gitian image](#setting-up-the-gitian-image)


Setting up Fedora for Gitian building
--------------------------------------

In this section we will be setting up the Fedora installation for Gitian building.
We assume that a user `gitian_user` was previously created and added to the `sudo` group.

To repeat ths step, log in as `root` and type:

```bash
usermod gitian_user -a -G wheel
```

Log in as `gitian_user` and set up the required dependencies.
Type/paste the following in the terminal:

```bash
sudo dnf install git python ruby apt-cacher-ng qemu qemu-kvm dpkg debootstrap python-cheetah gnupg tar rsync wget curl
```

Installing Gitian
------------------

There is no `python-vm-builder` package in Fedora, so we need to install it from source ourselves,

```bash
wget http://archive.ubuntu.com/ubuntu/pool/universe/v/vm-builder/vm-builder_0.12.4+bzr494.orig.tar.gz
echo "76cbf8c52c391160b2641e7120dbade5afded713afaa6032f733a261f13e6a8e  vm-builder_0.12.4+bzr494.orig.tar.gz" | sha256sum -c
# (verification -- must return OK)
tar -zxvf vm-builder_0.12.4+bzr494.orig.tar.gz
cd vm-builder-0.12.4+bzr494
sudo python setup.py install
cd ..
```

**Note**: When sudo asks for a password, enter the password for the user `gitian_user` not for `root`.

Clone the git repositories for bitcoin and Gitian.

```bash
git clone https://github.com/devrandom/gitian-builder.git
git clone https://github.com/bitcoin/bitcoin
git clone https://github.com/bitcoin-core/gitian.sigs.git
```

Setting up the Gitian image
-------------------------

Gitian needs a virtual image of the operating system to build in.
Currently this is Ubuntu Trusty x86_64.
This image will be copied and used every time that a build is started to
make sure that the build is deterministic.
Creating the image will take a while, but only has to be done once.

Execute the following as user `gitian_user`:

```bash
cd gitian-builder
bin/make-base-vm --arch amd64 --suite trusty
```

There will be a lot of warnings printed during the build of the image. These can be ignored.

**Note**: When sudo asks for a password, enter the password for the user *gitian_user* not for *root*.
