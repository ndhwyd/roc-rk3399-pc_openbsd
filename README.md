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
Alternatively you can download patched miniroot67.img from Releases


The utility will create a file "miniroot.img" in its own directory.
* Flash miniroot.img to microsd card with any preferred software, eg. [balenaEtcher](https://www.balena.io/etcher/)
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
* Install OpenBSD: Follow the steps of the OpenBSD installer
* After successfull installation reboot OpenBSD

### Step 7 - Have fun with OpenBSD 6.7 on ROC-RK3399-PC!


## Output (dmesg)

```
$ dmesg
OpenBSD 6.7-current (GENERIC.MP) #792: Sun Aug 30 20:42:47 MDT 2020
    deraadt@arm64.openbsd.org:/usr/src/sys/arch/arm64/compile/GENERIC.MP
real mem  = 4025335808 (3838MB)
avail mem = 3826872320 (3649MB)
random: good seed from bootblocks
mainbus0 at root: Firefly ROC-RK3399-PC Board
psci0 at mainbus0: PSCI 1.1, SMCCC 1.2
cpu0 at mainbus0 mpidr 0: ARM Cortex-A53 r0p4
cpu0: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu0: 512KB 64b/line 16-way L2 cache
cpu1 at mainbus0 mpidr 1: ARM Cortex-A53 r0p4
cpu1: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu1: 512KB 64b/line 16-way L2 cache
cpu2 at mainbus0 mpidr 2: ARM Cortex-A53 r0p4
cpu2: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu2: 512KB 64b/line 16-way L2 cache
cpu3 at mainbus0 mpidr 3: ARM Cortex-A53 r0p4
cpu3: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu3: 512KB 64b/line 16-way L2 cache
cpu4 at mainbus0 mpidr 100: ARM Cortex-A72 r0p2
cpu4: 48KB 64b/line 3-way L1 PIPT I-cache, 32KB 64b/line 2-way L1 D-cache
cpu4: 1024KB 64b/line 16-way L2 cache
cpu5 at mainbus0 mpidr 101: ARM Cortex-A72 r0p2
cpu5: 48KB 64b/line 3-way L1 PIPT I-cache, 32KB 64b/line 2-way L1 D-cache
cpu5: 1024KB 64b/line 16-way L2 cache
efi0 at mainbus0: UEFI 2.8
efi0: Das U-Boot rev 0x20201000
apm0 at mainbus0
agintc0 at mainbus0 sec shift 3:3 nirq 288 nredist 6 ipi: 0, 1: "interrupt-controller"
agintcmsi0 at agintc0
syscon0 at mainbus0: "qos"
syscon1 at mainbus0: "qos"
syscon2 at mainbus0: "qos"
syscon3 at mainbus0: "qos"
syscon4 at mainbus0: "qos"
syscon5 at mainbus0: "qos"
syscon6 at mainbus0: "qos"
syscon7 at mainbus0: "qos"
syscon8 at mainbus0: "qos"
syscon9 at mainbus0: "qos"
syscon10 at mainbus0: "qos"
syscon11 at mainbus0: "qos"
syscon12 at mainbus0: "qos"
syscon13 at mainbus0: "qos"
syscon14 at mainbus0: "qos"
syscon15 at mainbus0: "qos"
syscon16 at mainbus0: "qos"
syscon17 at mainbus0: "qos"
syscon18 at mainbus0: "qos"
syscon19 at mainbus0: "qos"
syscon20 at mainbus0: "qos"
syscon21 at mainbus0: "qos"
syscon22 at mainbus0: "qos"
syscon23 at mainbus0: "qos"
syscon24 at mainbus0: "qos"
syscon25 at mainbus0: "power-management"
"power-controller" at syscon25 not configured
syscon26 at mainbus0: "syscon"
"io-domains" at syscon26 not configured
rkclock0 at mainbus0
rkclock1 at mainbus0
syscon27 at mainbus0: "syscon"
"io-domains" at syscon27 not configured
"usb2-phy" at syscon27 not configured
"usb2-phy" at syscon27 not configured
rkemmcphy0 at syscon27
rkpinctrl0 at mainbus0: "pinctrl"
rkgpio0 at rkpinctrl0
rkgpio1 at rkpinctrl0
rkgpio2 at rkpinctrl0
rkgpio3 at rkpinctrl0
rkgpio4 at rkpinctrl0
pwmreg0 at mainbus0
syscon28 at mainbus0: "syscon"
syscon29 at mainbus0: "syscon"
"fit-images" at mainbus0 not configured
rkdrm0 at mainbus0
drm0 at rkdrm0
"pmu_a53" at mainbus0 not configured
"pmu_a72" at mainbus0 not configured
agtimer0 at mainbus0: tick rate 24000 KHz
"xin24m" at mainbus0 not configured
simplebus0 at mainbus0: "bus"
"dma-controller" at simplebus0 not configured
"dma-controller" at simplebus0 not configured
dwge0 at mainbus0: address ff:ff:ff:ff:ff:ff
rgephy0 at dwge0 phy 0: RTL8169S/8110S/8211 PHY, rev. 5
dwmmc0 at mainbus0: 50 MHz base clock
sdmmc0 at dwmmc0: 4-bit, sd high-speed, dma
sdhc0 at mainbus0
sdhc0: SDHC 3.0, 200 MHz base clock
sdmmc1 at sdhc0: 8-bit, sd high-speed, mmc high-speed, dma
ehci0 at mainbus0
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 configuration 1 interface 0 "Generic EHCI root hub" rev 2.00/1.00 addr 1
ohci0 at mainbus0: version 1.0
ehci1 at mainbus0
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 configuration 1 interface 0 "Generic EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at mainbus0: version 1.0
rkdwusb0 at mainbus0: "usb"
xhci0 at rkdwusb0, xHCI 1.10
usb2 at xhci0: USB revision 3.0
uhub2 at usb2 configuration 1 interface 0 "Generic xHCI root hub" rev 3.00/1.00 addr 1
rkdwusb1 at mainbus0: "usb"
xhci1 at rkdwusb1, xHCI 1.10
usb3 at xhci1: USB revision 3.0
uhub3 at usb3 configuration 1 interface 0 "Generic xHCI root hub" rev 3.00/1.00 addr 1
"saradc" at mainbus0 not configured
rkiic0 at mainbus0
iic0 at rkiic0
rkiic1 at mainbus0
iic1 at rkiic1
rkiic2 at mainbus0
iic2 at rkiic2
fusbtc0 at iic2 addr 0x22
"mps,mp8859" at iic2 addr 0x66 not configured
com0 at mainbus0: ns16550, no working fifo
com1 at mainbus0: ns16550, no working fifo
com1: console
"spi" at mainbus0 not configured
rktemp0 at mainbus0
rkiic3 at mainbus0
iic3 at rkiic3
rkpmic0 at iic3 addr 0x1b: RK808
fanpwr0 at iic3 addr 0x40: SYR827, 1.00 VDC
fanpwr1 at iic3 addr 0x41: SYR828, 1.00 VDC
rkiic4 at mainbus0
iic4 at rkiic4
fusbtc1 at iic4 addr 0x22
rkpwm0 at mainbus0
rkpwm1 at mainbus0
"video-codec" at mainbus0 not configured
"iommu" at mainbus0 not configured
"rga" at mainbus0 not configured
"efuse" at mainbus0 not configured
"phy" at mainbus0 not configured
"phy" at mainbus0 not configured
"watchdog" at mainbus0 not configured
"rktimer" at mainbus0 not configured
rkiis0 at mainbus0
rkiis1 at mainbus0
rkiis2 at mainbus0
rkvop0 at mainbus0: RK3399 VOPL
"iommu" at mainbus0 not configured
rkvop1 at mainbus0: RK3399 VOPB
"iommu" at mainbus0 not configured
"iommu" at mainbus0 not configured
"iommu" at mainbus0 not configured
simpleaudio0 at mainbus0
rkdwhdmi0 at mainbus0: HDMI TX
rkdwhdmi0: version 2.11a, phytype 0xf3
"gpu" at mainbus0 not configured
"opp-table0" at mainbus0 not configured
"opp-table1" at mainbus0 not configured
"opp-table2" at mainbus0 not configured
pwmbl0 at mainbus0
"external-gmac-clock" at mainbus0 not configured
"adc-keys" at mainbus0 not configured
"gpio-keys" at mainbus0 not configured
"leds" at mainbus0 not configured
"sdio-pwrseq" at mainbus0 not configured
"vcc-vbus-typec0" at mainbus0 not configured
"vcc1v8-s3" at mainbus0 not configured
"vcc3v0-sd" at mainbus0 not configured
"vcc3v3-sys" at mainbus0 not configured
"vcca-0v9" at mainbus0 not configured
"vcc5v0-host-regulator" at mainbus0 not configured
"vcc-vbus-typec1" at mainbus0 not configured
"vcc-sys" at mainbus0 not configured
"binman" at mainbus0 not configured
"dfi" at mainbus0 not configured
"dmc" at mainbus0 not configured
"config" at mainbus0 not configured
"vcc_hub_en-regulator" at mainbus0 not configured
usb4 at ohci0: USB revision 1.0
uhub4 at usb4 configuration 1 interface 0 "Generic OHCI root hub" rev 1.00/1.00 addr 1
usb5 at ohci1: USB revision 1.0
uhub5 at usb5 configuration 1 interface 0 "Generic OHCI root hub" rev 1.00/1.00 addr 1
scsibus0 at sdmmc0: 2 targets, initiator 0
sd0 at scsibus0 targ 1 lun 0: <SD/MMC, SB64G, 0080> removable
sd0: 60906MB, 512 bytes/sector, 124735488 sectors
sdmmc1: can't enable card
uhub6 at uhub1 port 1 configuration 1 interface 0 "Terminus Technology USB 2.0 Hub [MTT]" rev 2.00/1.00 addr 2
vscsi0 at root
scsibus1 at vscsi0: 256 targets
softraid0 at root
scsibus2 at softraid0: 256 targets
bootfile: sd0a:/bsd
boot device: sd0
root on sd0a (16be86d0728e7175.a) swap on sd0b dump on sd0b
WARNING: bad clock chip time
WARNING: CHECK AND RESET THE DATE!
rkvop0: using CRTC 0 for RK3399 VOPL
rkvop1: using CRTC 1 for RK3399 VOPB
rkdrm0: 1024x768, 32bpp
wsdisplay0 at rkdrm0 mux 1
wsdisplay0: screen 0-5 added (std, vt100 emulation)
```

## Limitations

* HDMI output working only for X

## Credits:

Thanks to all people from U-Boot and the OpenBSD project.

Also thanks to Johannes Krottmayer for the original guide for ROCK64.
