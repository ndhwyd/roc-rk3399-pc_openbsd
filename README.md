# TUTORIAL: Install OpenBSD 6.5 on a PINE64 ROCK64 media board 

**Required hardware**

* PC with Linux or OpenBSD intalled
* ROCK64 media board
* USB-UART-TTL converter (**Attention:** Use 3.3V only)
* microSD card

**Required software images**

* UART Terminal (in this tutorial I use minicom)
* Ayufan's U-Boot SPI Flash image

[Download (latest release)](https://github.com/ayufan-rock64/linux-u-boot/releases/download/2017.09-rockchip-ayufan-1045-g9922d32c04/u-boot-flash-spi-rock64.img.xz)
* miniroot65.fs image for ARM64

[Download](https://ftp2.eu.openbsd.org/pub/OpenBSD/6.5/arm64/miniroot65.fs)

### Step 1 - Flash U-Boot in the SPI-EEPROM on the ROCK64 board 

* Connect microSD card with your PC
* Open a terminal on your PC
* Extract Ayufan's U-Boot SPI Flash image
```
$ unxz u-boot-flash-spi-rock64.img.xz
```
* Copy the extracted image to microSD card
```
$ dd if=u-boot-flash-spi-rock64.img of=/dev/sdx bs=1M
```
* Remove the microSD card from your PC
* Put microSD card in ROCK64
* Connect USB-UART-TTL converter with ROCK64
* Start the UART terminal with baud rate 1500000
```
$ minicom -D /dev/ttyUSB0 -b 1500000 -8
```
* Power-On ROCK64
* Wait until you see in the last line "SF: 4096000 bytes @ 0x8000 Written: OK"

![alt text](https://github.com/krjdev/rock64_openbsd/blob/master/img/spi_flash.png)

* Power-Down ROCK64
* Remove the MicroSD from ROCK64

### Step 2 - Install OpenBSD

* Connect the microSD card with your PC
* Open the terminal on your PC
* Copy miniroot65.fs to microSD

```
$ dd if=miniroot65.fs of=/dev/sdx bs=1M
```

* Remove microSD card from PC
* Put microSD card in the ROCK64
* Start minicom with the baud rate 115200
```
minicom -8 -D /dev/ttyUSB0 -b 115200
```
* Power-On the ROCK64
* Wait until you see the OpenBSD Installer:

![alt text](https://github.com/krjdev/rock64_openbsd/blob/master/img/openbsd_installer.png)

* Install OpenBSD: Follow the steps of the OpenBSD installer
* After successfull installation reboot OpenBSD

### Step 3 - Have fun with OpenBSD on the ROCK64!

![alt text](https://github.com/krjdev/rock64_openbsd/blob/master/img/openbsd_welcome.png)

**dmesg output after install:**

```
$ dmesg
OpenBSD 6.5 (GENERIC.MP) #84: Wed Apr 17 05:53:43 MDT 2019
    deraadt@arm64.openbsd.org:/usr/src/sys/arch/arm64/compile/GENERIC.MP
real mem  = 4213956608 (4018MB)
avail mem = 4051111936 (3863MB)
mainbus0 at root: Pine64 Rock64
cpu0 at mainbus0 mpidr 0: ARM Cortex-A53 r0p4
cpu0: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu0: 256KB 64b/line 16-way L2 cache
efi0 at mainbus0: UEFI 2.0.5
efi0: Das U-boot rev 0x0
apm0 at mainbus0
psci0 at mainbus0: PSCI 1.0
syscon0 at mainbus0: "syscon"
syscon1 at mainbus0: "power-management"
rkclock0 at mainbus0
rkclock_set_parent: 0x000000b4
rkclock_set_frequency: 0x000000c4
rkclock_set_frequency: 0x000000c5
rkclock_set_frequency: 0x000000ca
rkclock_set_frequency: 0x000000c1
rkclock_set_frequency: 0x000000bf
rkclock_set_frequency: 0x000000c6
rkclock_set_frequency: 0x000000c8
rkclock_set_frequency: 0x000000c9
rkclock_set_frequency: 0x000000c4
rkclock_set_frequency: 0x000001ac
rkclock_set_frequency: 0x0000013c
rkclock_set_frequency: 0x000000c5
rkclock_set_frequency: 0x00000198
rkclock_set_frequency: 0x0000014a
rkclock_set_frequency: 0x000000ca
rkclock_set_frequency: 0x000001a9
rkclock_set_frequency: 0x000000c1
rkclock_set_frequency: 0x00000045
rkclock_set_frequency: 0x000000bf
rkclock_set_frequency: 0x000000c6
rkclock_set_frequency: 0x000000c8
rkclock_set_frequency: 0x000000c9
rkclock_set_frequency: 0x0000003e
rkclock_set_frequency: 0x00000149
rkclock_set_frequency: 0x000000ce
rkclock_set_frequency: 0x00000140
rkclock_set_frequency: 0x00000061
syscon2 at mainbus0: "syscon-usb"
"usb2-phy" at syscon2 not configured
ampintc0 at mainbus0 nirq 160, ncpu 4 ipi: 0, 1: "interrupt-controller"
rkpinctrl0 at mainbus0: "pinctrl"
rkgpio0 at rkpinctrl0
rkgpio1 at rkpinctrl0
rkgpio2 at rkpinctrl0
rkgpio3 at rkpinctrl0
agtimer0 at mainbus0: tick rate 24000 KHz
com0 at mainbus0: ns16550, no working fifo
com0: console
rkiic0 at mainbus0
iic0 at rkiic0
rkpmic0 at iic0 addr 0x18: RK805
simplebus0 at mainbus0: "amba"
"dmac" at simplebus0 not configured
dwmmc0 at mainbus0: 50 MHz base clock
sdmmc0 at dwmmc0: 8-bit, mmc high-speed, dma
dwmmc1 at mainbus0: 50 MHz base clock
sdmmc1 at dwmmc1: 4-bit, sd high-speed, mmc high-speed, dma
dwge0 at mainbus0
dwge0: address: 12:a1:57:33:b4:3a
rgephy0 at dwge0 phy 0: RTL8169S/8110S/8211 PHY, rev. 6
ehci0 at mainbus0
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 configuration 1 interface 0 "Generic EHCI root hub" rev 2.00/1.00 addr 1
ohci0 at mainbus0: version 1.0
cpu1 at mainbus0 mpidr 1: ARM Cortex-A53 r0p4
cpu1: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu1: 256KB 64b/line 16-way L2 cache
cpu2 at mainbus0 mpidr 2: ARM Cortex-A53 r0p4
cpu2: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu2: 256KB 64b/line 16-way L2 cache
cpu3 at mainbus0 mpidr 3: ARM Cortex-A53 r0p4
cpu3: 32KB 64b/line 2-way L1 VIPT I-cache, 32KB 64b/line 4-way L1 D-cache
cpu3: 256KB 64b/line 16-way L2 cache
usb1 at ohci0: USB revision 1.0
uhub1 at usb1 configuration 1 interface 0 "Generic OHCI root hub" rev 1.00/1.00 addr 1
scsibus0 at sdmmc1: 2 targets, initiator 0
sd0 at scsibus0 targ 1 lun 0: <SD/MMC, SC32G, 0080> SCSI2 0/direct removable
sd0: 30436MB, 512 bytes/sector, 62333952 sectors
scsibus1 at sdmmc0: 2 targets, initiator 0
sd1 at scsibus1 targ 1 lun 0: <SD/MMC, NCard, 0000> SCSI2 0/direct removable
sd1: 59000MB, 512 bytes/sector, 120832000 sectors
vscsi0 at root
scsibus2 at vscsi0: 256 targets
softraid0 at root
scsibus3 at softraid0: 256 targets
bootfile: sd0a:/bsd
boot device: sd0
root on sd1a (27151c8abf92b0dd.a) swap on sd1b dump on sd1b
WARNING: preposterous time in file system
WARNING: CHECK AND RESET THE DATE!
cpu0: clock not implemented
$
```

##### Limitations

* HDMI output currently not working

##### Credits:

Thanks to Ayufan, Mark Kettenis and all people of the OpenBSD project .
