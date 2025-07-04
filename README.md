# Linux kernel for NanoPi M4/M4v2

## Kernel version
6.15.4

## How to update
```bash
# UBoot
sudo cp uboot/configs/* /boot/

# UBoot(M4v1)
sudo dd if=uboot/m4v1/idbloader.bin of=/dev/mmcblk2 seek=64
sudo dd if=uboot/m4v1/uboot.img of=/dev/mmcblk2 seek=16384
sudo dd if=uboot/m4v1/trust.bin of=/dev/mmcblk2 seek=24576

# UBoot(M4v2)
sudo dd if=uboot/m4v2/idbloader.bin of=/dev/mmcblk2 seek=64
sudo dd if=uboot/m4v2/uboot.img of=/dev/mmcblk2 seek=16384
sudo dd if=uboot/m4v2/trust.bin of=/dev/mmcblk2 seek=24576

# Linux
sudo cp -a boot/* /boot/
sudo cp -a lib/modules/* /lib/modules/
sudo mkinitcpio -g /boot/initrd.img-6.15.4 -k 6.15.4
sudo mkimage -A arm -O linux -T ramdisk -C none -a 0 -e 0 -n uInitrd -d /boot/initrd.img-6.15.4 /boot/uInitrd-6.15.4
sudo rm -f /boot/dtb /boot/Image /boot/uInitrd
sudo ln -sf dtb-6.15.4 /boot/dtb
sudo ln -sf vmlinuz-6.15.4 /boot/Image
sudo ln -sf uInitrd-6.15.4 /boot/uInitrd
sudo chown -R root:root /boot /lib/modules
```

## How to build kernel
```bash
git clone --depth 1 -b nanopi-m4 https://github.com/heiher/linux
cd linux
wget -O .config https://github.com/heiher/kernel-nanopi-m4/raw/master/boot/config-6.15.4
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- DTC_FLAGS="-@" dtbs
```
