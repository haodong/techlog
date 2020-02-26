## Prepare Files

1. Open [official page](http://downloads.openwrt.org/releases/19.07.1/targets/x86/64/).
2. Choose [generic-rootfs.tar.gz](http://downloads.openwrt.org/releases/19.07.1/targets/x86/64/openwrt-19.07.1-x86-64-generic-rootfs.tar.gz) and [vmlinuz](http://downloads.openwrt.org/releases/19.07.1/targets/x86/64/openwrt-19.07.1-x86-64-vmlinuz).
3. Open Hyper-V, and create a gen-2 VM.
4. Create a dynamic VHDX with a >1GB size.

## Build Disk

1. Attach the vhdx to another linux system.
2. Use `lsblk` to spot its drive, like `/dev/sdc`.
```bash
~# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0    7:0    0 89.1M  1 loop /snap/core/8268
loop2    7:2    0 91.3M  1 loop /snap/core/8592
sda      8:0    0    8G  0 disk
├─sda1   8:1    0  512M  0 part /boot/efi
└─sda2   8:2    0  7.5G  0 part /
sdb      8:16   0   64G  0 disk
└─sdb1   8:17   0 63.5G  0 part /data
sdc      8:32   0    1G  0 disk
```
3. Partition and format the disk.
```bash
~# parted --script /dev/sdc \
            unit MiB \
            mklabel gpt \
            mkpart ESI fat32 1 129 \
            mkpart primary ext4 129 100% \
            set 1 boot on \
            quit
            
~# mkfs.vfat -F 32 /dev/sdc1
mkfs.fat 4.1 (2017-01-24)

~# mkfs.ext4 /dev/sdc2
mke2fs 1.44.1 (24-Mar-2018)
Discarding device blocks: done
Creating filesystem with 228864 4k blocks and 57232 inodes
Filesystem UUID: 9986de75-5328-476b-9394-d02bf8cad43d
Superblock backups stored on blocks:
        32768, 98304, 163840

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

~# parted /dev/sdc print
Model: Msft Virtual Disk (scsi)
Disk /dev/sdc: 1074MB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size   File system  Name     Flags
 1      1049kB  135MB   134MB  fat32        ESI      boot, esp
 2      135MB   1073MB  937MB  ext4         primary
```
4. Configure boot.
```bash
~# mkdir -p /mnt/sdc1
~# mkdir -p /mnt/sdc2
~# mount /dev/sdc1 /mnt/sdc1
~# mount /dev/sdc2 /mnt/sdc2

~# bootctl --path=/mnt/sdc1 install
~# bootctl --path=/mnt/sdc1 install
Created "/mnt/sdc1/EFI".
Created "/mnt/sdc1/EFI/systemd".
Created "/mnt/sdc1/EFI/BOOT".
Created "/mnt/sdc1/loader".
Created "/mnt/sdc1/loader/entries".
Copied "/usr/lib/systemd/boot/efi/systemd-bootx64.efi" to "/mnt/sdc1/EFI/systemd/systemd-bootx64.efi".
Copied "/usr/lib/systemd/boot/efi/systemd-bootx64.efi" to "/mnt/sdc1/EFI/BOOT/BOOTX64.EFI".
Created EFI boot entry "Linux Boot Manager".

~# sed -i 's|#timeout 3|timeout 0|; s|default .*|default openwrt|' /mnt/sdc1/loader/loader.conf
~# echo "title OpenWrt" >> /mnt/sdc1/loader/entries/openwrt.conf
~# echo "linux /vmlinuz" >> /mnt/sdc1/loader/entries/openwrt.conf
~# echo "options ro root=/dev/sda2 rootfstype=ext4 rootwait console=tty0" >> /mnt/sdc1/loader/entries/openwrt.conf

~# cp openwrt-19.07.1-x86-64-vmlinuz /mnt/sdc1/vmlinuz
~# tar -zx -C /mnt/sdc2 -f openwrt-19.07.1-x86-64-generic-rootfs.tar.gz

~# umount /dev/sdc1
~# umount /dev/sdc2
~# eject /dev/sdc
```
## Start the VM.
1. Detach the vmdx from linux.
2. Attach the vmdx to OpenWrt.
3. Start the vm of OpenWrt.
4. Press _Enter_ to enjoy.
## Reference
1. [How to install OpenWrt on Hyper-V Gen2 VM](https://qiita.com/kagurazakakotori/items/57243b855df57519510b)
