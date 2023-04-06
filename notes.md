I will be doing this project from a 42 School iMac (on macOS).

## Creating the VM that will create the Containers
### Alpine vs. Debian
Before deciding which OS to use, let's compare their characteristics:
- Debian
- - Much more widely used
- - Heavier
- - Longer cycles mean older software versions
- Alpine
- - Focus on light weight and security

I decided to use Alpine as it is new for me and is lightweight, which should speed things up. It also seems to include newer software packages, and stability will not be an issue for this project.

#### Penultimate Stable Versions (25.03.2023)
The versions that are meant to be used for this project are the penultimate stable ones available:
- [Debian Releases](https://www.debian.org/releases/): "Bullseye" 11.x latest -> "Buster" 10.13 penultimate
- [Alpine Releases](https://www.alpinelinux.org/releases/): 3.17 latest -> 3.16 penultimate
For Alpine, this means the Alpine 3.16 version.

### Virtual Machine Set-Up
I created a [bash script](https://github.com/pandaero/42_Core_inception/blob/master/virtual_machine.sh) to reliably set-up the virtual machine on VirtualBox (macOS) from scratch, and perform the clearing of it for "hard resets".

The script will do the following:
- Download the image of the OS (requires wget)
- Create and register the VM in VirtualBox (requires VirtualBox)
The script will check for the image to be correctly downloaded. It includes a VM-starting section, however the VM is going to be set-up using the VirtualBox GUI as well.

#### Operating System Set-Up
It is a good idea to save snapshots of the machine at key stages of the set-up. The first would be after successfully booting up for the first time.
##### `setup-alpine` Command
This command will provide a guided set-up for system elements (used values in brackets):
- input (us - us keyboard)
- hostname (localhost) - default {press `Enter`}
- network (default) - default
- root user password (take note, uppercase + lowercase + digit)
- timezone (UTC) - default
- network proxy (none) - default
- NTP client (chrony) - default
- system download mirrors (f)
- Additional user (loginname / password)
- ssh server (openssh) - default
- machine disk (virtual machine hard disk)
- disk partitioning (lvmsys)
After running the command and creating the partitions, we will reboot the machine (`reboot` command). Note that the first time we booted from the OS image. Now we will want to boot from the machine hard disk. A great point for a second snapshot is after this boot.

##### Network Interfaces
Very importantly, the network interface must be set-up ([documentation](https://wiki.alpinelinux.org/wiki/Configure_Networking)), this should have been done by the `setup-alpine` command, but to do this manually we would edit the `/etc/network/interfaces` and add the ethernet adapter with the following lines:
```
auto eth0
iface eth0 inet dhcp
```
Then apply the changes by running:
```
/etc/init.d/networking restart
```

##### Package Manager
The package manager APK must be configured before being able to install packages. Again, `setup-alpine` should have configured this, though not the specific version mirrors. We are interested in the `nano`, `make`, `docker`, `docker-compose`, and potentially other packages. We can edit `/etc/apk/repositories` and add mirrors:
```
http://dl-cdn.alpinelinux.org/alpine/v3.16/main
http://dl-cdn.alpinelinux.org/alpine/v3.16/community
```
Now we can install packages and run the VM normally.

## Creating the Containers
The packages `docker` and `docker-compose` are required for this. `make` is required to use the Makefile.

### Docker images
There are docker images with the operating systems ready, these are:
[Debian Images](https://hub.docker.com/_/debian)
[Alpine Images](https://hub.docker.com/_/alpine)

#### Alpine Docker Image
The dockerfile to set-up a container running the vanilla Alpine 3.16 operating system looks like this (good to know, but we will set up our containers `FROM Alpine 3.16` instead of `FROM scratch`):
```Dockerfile
FROM scratch
ADD alpine-minirootfs-3.16.4-x86_64.tar.gz /
CMD ["/bin/sh"]
```