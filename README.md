# Tools for Armbian Images

## image_exec

This tool is used to execute a command on a Armbian image file. It mounts the image file, executes the command and unmounts the image file again. It is used to execute commands on the image file before it is flashed to the SD card.

### Requires
- ```apt install qemu-user-static```

### Usage

- ```sudo ./image_exec -i <image> -p <partition_number> -u <command> [more commands]```
- ```sudo ./image_exec -i <input_image> -o <output_image> -p <partition_number> <command> [more commands]```

### Examples

- ```sudo ./image_exec -i armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img -p 1 -u "apt update && apt upgrade"```
- ```sudo ./image_exec -i armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8.img -o armbian_5.38_Orangepizero_Ubuntu_bionic_default_4.19.8_updated.img -p 1 "apt update && apt upgrade"```