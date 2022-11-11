# Tools for Armbian Images

## image_exec

This tool is used to execute a command on an Armbian image file. It mounts the image file, executes the command and unmounts the image file again. It is used to execute commands on the image file before it is flashed to the SD card.

### Requires
- ```apt install qemu-user-static```

### Usage

- ```sudo ./image_exec -i <image> -p <partition_number> -u <command> [more commands]```
- ```sudo ./image_exec -i <input_image> -o <output_image> -p <partition_number> <command> [more commands]```

### Examples

- ```sudo ./image_exec -i armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img -p 1 -u "apt update && apt upgrade"```
- ```sudo ./image_exec -i armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img -o armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8_updated.img -p 1 "apt update && apt upgrade"```

### Options

```
-i <image>        Input image file
-o <image>        Output image file
-p <partition>    Partition number to mount (default: 1)
-u                Update the input image, instead of generating a new output image
```

## qemu_boot

This tools is used to run an Armbian image in QEMU. It's tested with qemu-system-aarch64 in an amd64 host. Both the original Armbian image and Amlogic Armbian (ext4 and btrfs root filesystem) from https://github.com/ophub/amlogic-s9xxx-armbian are supported.

### Features

- Running Armbian in single user mode, avoided the initial setup process of Armbian.
- NAT network enabled, so you can run apt update & install in the image.

### Requires
- ```apt install qemu-system-aarch64 u-boot-qemu```

### Usage
- Build flash images

```
make -C qemu_flash clean
make -C qemu_flash
```

- Run Armbian image in QEMU

```
sudo qemu_boot [options] <image path>
```

### Options

```
-h, --help
    Show this help message and exit

-m <memory size>
    Memory size for QEMU in MB, default is 1024

-c <cores>
    Number of cores for QEMU, default is 1

-e <ethernet MAC address>
    Ethernet MAC address for QEMU, default is random

-v <0/1>
    Enable/disable virtio devices, default is 0 (disable)
```

## Howto

### Howto install apt packages in Armbian image

To install apt packages (e.g. snapd) in an Armbian image, just use the image_exec tool.

```
sudo ./image_exec -i armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img -p 1 -u "apt update && apt install -y snapd"
```

### Howto install snap packages in Armbian image

Snap installation requires running services including apparmor, snapd, snapd.socket and snapd.seeded.service. To run these services, you need to run the image in QEMU.

```qemu_boot``` starts Linux kernel into run level 1, so we can walk around the initial setup process of Armbian. After disabling armbian-firstlogin, we can switch to runlevel 3 and start the services.


- Build flash images

```
make -C qemu_flash clean
make -C qemu_flash
```

- Run Armbian image in QEMU

```
sudo qemu_boot armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img
```

- Disable armbian-firstlogin

```
rm /root/.not_logged_in_yet
```

- Switch to runlevel 3

```
sudo telinit 3
```

- Install snapd

```
sudo apt update
sudo apt install -y snapd
```

- Install snap packages (this may take quite a while)


```
sudo snap install lxd
```

- Re-enable armbian-firstlogin

```
touch /root/.not_logged_in_yet
```

- Exit QEMU

```
poweroff
```
