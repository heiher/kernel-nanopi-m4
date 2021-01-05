# Linux kernel for NanoPi M4/M4v2

**Important**: Make sure the CPU frequency governor on the NanoPi M4v2 is set to `performance`, otherwise the system may be unstable.

## Kernel version
6.19.11

## How to update
```bash
# The first 16 MB of the TF card is reserved for the U-Boot bootloader,
# so the first partition starts at 16 MB (512 × 32768).

# UBoot
cp uboot/configs/* /boot/

# UBoot(M4v1)
dd if=uboot/m4v1/idbloader.bin of=/dev/mmcblk2 seek=64
dd if=uboot/m4v1/uboot.img of=/dev/mmcblk2 seek=16384
dd if=uboot/m4v1/trust.bin of=/dev/mmcblk2 seek=24576

# UBoot(M4v2)
dd if=uboot/m4v2/idbloader.bin of=/dev/mmcblk2 seek=64
dd if=uboot/m4v2/uboot.img of=/dev/mmcblk2 seek=16384
dd if=uboot/m4v2/trust.bin of=/dev/mmcblk2 seek=24576

# Linux
cp -a boot/* /boot/
cp -a lib/modules/* /lib/modules/
mkinitcpio -g /boot/initrd.img-6.19.11 -k 6.19.11
mkimage -A arm -O linux -T ramdisk -C none -a 0 -e 0 -n uInitrd -d /boot/initrd.img-6.19.11 /boot/uInitrd-6.19.11
rm -f /boot/dtb /boot/Image /boot/uInitrd
ln -sf dtb-6.19.11 /boot/dtb
ln -sf vmlinuz-6.19.11 /boot/Image
ln -sf uInitrd-6.19.11 /boot/uInitrd
chown -R root:root /boot /lib/modules
```

## How to build kernel
```bash
git clone --depth 1 -b nanopi-m4 https://github.com/heiher/linux
cd linux
wget -O .config https://github.com/heiher/kernel-nanopi-m4/raw/master/boot/config-6.19.11
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- DTC_FLAGS="-@" dtbs
```
