Crux 3.0 on Asus Zenbook u500vz/ux51vz
======================================

Intro
-----

TBD

Hardware
--------

<pre>
Processor:   Intel® Core™ i7 3612QM Processor
Chipset:     Intel® Chief River Chipset HM77
Memory:      8 GB
Display:     16:9 HD with IPS FHD (1920x1080) LED Backlight anti-glare
Graphic:     NVIDIA® GeForce® GT 650M 2GB GDDR5 VRAM
Storage:     2 x 256GB SSD A-Data XM11 (RAID0 Technology)
Card Reader: 2-in-1 card reader (SD/MMC)
Camera:	     HD 720p CMOS with Array Microphone
Wifi & BT:   Intel Advanced-N 6235(dual band, 2x2 Wi-Fi/BT combo HMC module)
Ethernet:    Atheros AR8151 Gigabit
Audio:       Built-in Speakers And Microphone (Additional subwoofer)
USB ports:   3 x 3.0
HDMI:        1
Mini VGA:    1
</pre>

Status
------
<pre>
Processor:   Works
Chipset:     Works
Memory:      Works
Graphic:     Works
Storage:     Works
Card Reader: Not Tested
Camera:      Not Tested
Wifi:        Works
BT:          Not Tested
Ethernet:    Works
Audio:       Not Tested
USB ports:   Works (as 2.0, 3.0 Not Tested)
HDMI:        Not Tested
Mini VGA:    Not Tested
</pre>

Preparation
-----------

TBD

Installation
------------

1. Remove all /dev/md* with `mdadm -S /dev/mdX'
2. Clear GUID partition table  of /dev/sd[ab] if exists with:
<pre>
# parted /dev/sdX
mklabel msdos
quit
</pre>
3. Make with fdisk partition scheme (Fist cylinder of first partition is 2):
<pre>
# fdisk -H 224 -S 56 /dev/sda
# fdisk -l /dev/sda
# dd if=/dev/sda of=/dev/sdb count=1
</pre>
4. Create software raid:
<pre>
# mdadm -C /dev/md0 -c 128 -n 2 -e 0.9 -l 1 /dev/sda1 /dev/sdb1
# mdadm -C /dev/md1 -c 128 -n 2 -e 0.9 -l 0 /dev/sda2 /deb/sdb2
# mdadm -C /dev/md2 -c 128 -n 2 -e 0.9 -l 0 /dev/sda3 /dev/sdb3
# mdadm -C /dev/md3 -c 128 -n 2 -e 0.9 -l 0 /dev/sda4 /dev/sdb4
</pre>
5. Create file systems:
<pre>
# mkfs.ext4 /dev/md0
# mkfs.ext4 /dev/md2
# mkfs.ext4 /dev/md3
# mkswap /dev/md1
</pre>
6. Mount new root:
<pre>
# mount -o noatime,nodiratime,discard,errors=remount-ro /dev/md2 /mnt
# mkdir /mnt/boot
# mount -o noatime,nodiratime,discard,errors=remount-ro /dev/md0 /mnt/boot
</pre>
7. Setup Crux. Note: install mdadm and don't install xorg-video-* except xorg-video-intel.
<pre>
# setup
</pre>
8. Chroot:
<pre>
# mount --bind /dev /mnt/dev
# mount --bind /tmp /mnt/tmp
# mount -t proc proc /mnt/proc
# mount -t sysfs none /mnt/sys
# chroot /mnt /bin/bash
</pre>
9. Edit sysctl.conf:
<pre>
vm.swappiness = 1
vm.vfs_cache_pressure = 25
vm.dirty_ratio = 40
vm.dirty_background_ratio = 3
</pre>
