# TUTORIAL: Install OpenBSD 6.7 on a ROC-RK3399-PC (Renegade Elite)

**Required hardware**

* PC with nix based OS or Windows installed (In this tutorial I use Windows 10 + WSL2)
* ROC-RK3399-PC board (In this tutorial I use version 2018 of the board)
* USB-UART-TTL converter (**Attention:** Use 3.3V only)

![alt text](/img/roc-rk3399-pc_uart_cp210x.jpg)

* microSD card

**Required software**

* UART Terminal (eg. minicom or Putty)
* GCC cross compiler for ARM64 (aarch64)
* Image *miniroot67.img* for ARM64 from the offical OpenBSD CDN

[Download](https://cdn.openbsd.org/pub/OpenBSD/snapshots/arm64/miniroot67.img)

### Step 1 - Build ATF (ARM Trusted Firmware)


*NOTE*  
In this tutorial I used the compiler from the Ubuntu repository installed with
``
sudo apt-get install gcc-aarch64-linux-gnu
``

* Checkout ATF sources  
``
$ git clone https://github.com/ARM-software/arm-trusted-firmware
``  
``
$ cd arm-trusted-firmware
``  
* Build ATF (BL31)  
``
$ make distclean
``  
``
$ make CROSS_COMPILE=aarch64-linux-gnu- PLAT=rk3399 bl31
``  
* Export ATF for U-Boot  
``
$ export BL31=/path/to/arm-trusted-firmware/build/rk3399/release/bl31/bl31.elf
``

*NOTE*  
The previous steps (build ATF) are required to successfully boot OpenBSD. Without these steps, U-Boot
will boot but cannot load OpenBSD.

### Step 2 - Build U-Boot


* Checkout U-Boot sources  
``
$ git clone https://github.com/u-boot/u-boot
``  
``
$ cd u-boot
``  
* Build U-Boot  
``
$ make mrproper
``  
``
$ make roc-pc-rk3399_defconfig
``  
``
$ make CROSS_COMPILE=aarch64-linux-gnu-
``

*NOTE*  
Alternatively you can use my prebuilt binaries (U-Boot v2020.10):

[ROC-RK3399-PC](/bin/roc-rk3399-pc/v2020.10)  

![alt text](/img/roc-rk3399-pc-u-boot_v2020.10.png)

### Step 3 - Install *miniroot67.img* on microSD card
*NOTE*
If you are using Windows, you can skip steps 3-5 and go to step 5a.

* Connect the microSD card with your PC
* Open the terminal on your PC
* Copy *miniroot67.img* to microSD

```
$ dd if=/path/to/miniroot67.img of=/dev/sdx bs=1M
```

### Step 4 - Place *idbloader.img* and *u-boot.itb* on microSD card

* Place *idbloader.img* on microSD card
```
$ dd if=/path/to/idbloader.img of=/dev/sdx bs=512 seek=64 conv=sync
```
* Place *u-boot.itb* on microSD card
```
$ dd if=/path/to/u-boot.itb of=/dev/sdx bs=512 seek=16384 conv=sync
```

### Step 5 - Place *rk3399-roc-pc.dtb* on microSD card

* Mount the FAT partition  
``
$ mount -t vfat /dev/sdx1 /mnt
``  
* Create a directory with the name rockchip  
``
$ cd /mnt
``  
``
$ mkdir rockchip
``  
* Place the dtb file in this directory  
``
$ cp path/to/u-boot/arch/arm/dts/rk3399-roc-pc.dtb ./rockchip
``  
* Unmount the microSD card and remove the card

### Step 5a - Patch miniroot67.img with idbloader and u-boot
*NOTE*
This step usable for Windows users only, because there is no dd utility in Windows.

* Connect the microSD card with your PC
* [Download openbsd-miniroot-patcher](https://github.com/ndhwyd/openbsd-miniroot-patcher)
* Combine miniroot67.img with idbloader.img and u-boot.itb into one file using the utility
```
MinirootPatcher.exe -m X:\path_to_miniroot67.img -i X:\path_to_idbloader.img -u X:\path_to_u-boot.itb
```
*NOTE*  
Alternatively you can use my miniroot.img:
[Download](/bin/roc-rk3399-pc/miniroot.img)  

The utility will create a file "miniroot.img" in its own directory.
* Flash miniroot.img to microsd card with any preferred software, eg. balenaEtcher(https://www.balena.io/etcher/)
* After copying, take out and insert the sdcard.
* Open the BOOT drive in explorer
* Create a folder named "rockchip" (no quotes)
* Copy the file "rk3399-roc-pc.dtb" to the created directory

### Step 6 - Install OpenBSD

* Put microSD card in the ROC-RK3399-PC
* Start your serial terminal software with the baud rate 1500000

minicom on Linux
```
minicom -8 -D /dev/ttyUSB0 -b 1500000
```

Putty on Windows
![alt text](/img/putty-settings.png)

* Power-On the ROC-RK3399-PC
* Wait until you see the OpenBSD Installer:

![alt text](/img/rock64-obsd_installer.png)

* Install OpenBSD: Follow the steps of the OpenBSD installer
* After successfull installation reboot OpenBSD

### Step 7 - Have fun with OpenBSD 6.7 on ROC-RK3399-PC!


![alt text](/img/rock64-obsd_welcome.png)

## Output (dmesg)

```
$ dmesg

```

## Limitations

* HDMI output working only for X

## Credits:

Thanks to all people from U-Boot and the OpenBSD project.

Also thanks to Johannes Krottmayer for the original guide for ROCK64.
