FLASH_SIZE=64M
DTB_BLK_OFFSET=512 # 256KB

all: output/flash0.bin output/flash1.bin output/virt.dtb

# memory map:
# 0x40000000: FDT of QEMU
# 0x40400000: kernel image
# 0x44000000: ramdisk image and temporary uEnv.txt

# u-boot.in can be found in apt package u-boot-qemu
output/flash0.bin: u-boot.bin
	truncate -s $(FLASH_SIZE) $@
	dd if=$< of=$@ conv=notrunc

output/flash1.bin: output/bootenv.bin
	truncate -s ${FLASH_SIZE} $@
	dd if=output/bootenv.bin of=$@ conv=notrunc

output/bootenv.bin: output/bootenv.txt
	mkenvimage -p 0 -s 0x40000 -o $@ $<

output/bootenv.txt:
	./generate_bootenv > $@

output/virt.dtb:
	qemu-system-aarch64 -machine virt -cpu cortex-a57 -machine dumpdtb=$@ -nographic

.PHONY: clean

clean:
	rm -f output/flash0.bin \
		output/flash1.bin \
		output/bootenv.bin \
		output/bootenv.txt \
		output/virt.dtb