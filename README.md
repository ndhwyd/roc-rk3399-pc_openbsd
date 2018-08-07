# TUTORIAL: Install OpenBSD on a ROCK64 media board 

**Required hardware**

* ROCK64 media board
* USB-RS232-TTL converter (**Attention:** Use 3.3V only)
* microSD card

**Required software tools**

* PINE64 Installer

Link: https://github.com/pine64dev/PINE64-Installer/blob/master/README.md

* ayufun's U-Boot SPI Flasher
* Terminal (like minicom)

### Step 1 - Flash U-Boot in SPI-EEPROM on the ROCK64

* Download the current PINE64 Installer
* Extract the PINE64 Installer
* Download the current U-Boot SPI flasher image from ayufan's github page here: 

Link: https://github.com/ayufan-rock64/linux-build/releases/download/0.6.51/u-boot-flash-spi-rock64.img.xz

* Connect the microSD card with your PC
* Execute the PINE64 Installer:

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_01.png "PINE64 Installer 1")

* Click on button **Choose an OS**

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_02.png "PINE64 Installer 2")

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_03.png "PINE64 Installer 3")

* Select **ROCK64** in the drop-down menu

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_04.png "PINE64 Installer 4")

* Select **Browse image file from local drive**

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_05.png "PINE64 Installer 5")

* Select the downloaded U-Boot SPI flasher image

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_06.png "PINE64 Installer 6")

* Choose the correct microSD card

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_07.png "PINE64 Installer 7")

* Click on the button **Flash**

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/pine64installer_08.png "PINE64 Installer 8")

* After successfully flashing close the PINE64 Installer and remove the microSD card
* Put the flashed microSD card in the ROCK64
* Connect the USB-RS232-TTL converter with the ROCK64:

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/rock64.png "ROCK64")

* Start minicom with the baud rate 1500000

```
$ minicom -8 -D /dev/ttyUSB0 -b 1500000
```

* Power-On the ROCK64
* Wait unil you see following:

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/flash.png "Flash")

* Power-Down the ROCK64
* Remove the MicroSD from ROCK64

### Step 2 - Install OpenBSD

* Connect the microSD card with your PC
* Download the file "miniroot63.fs" form the OpenBSD's mirrors

Link: https://ftp2.eu.openbsd.org/pub/OpenBSD/snapshots/arm64/ (Example link)

**INFO**

You must use the -CURRENT (snapshot) tree. With the release 6.3 of the ARM port of OpenBSD the Gigabit network of the ROCK64 doesn't work. 


* Open the terminal
* Type the following (as example - change the command where you have downloaded the file minirooot63.fs and the correct device for the microSD card):

```
$ dd if=miniroot63.fs of=/dev/sde
```

* Remove the microSD card from PC
* Put the microSD card in the ROCK64
* Start minicom with the baud rate 115200

```
minicom -8 -D /dev/ttyUSB0 -b 115200
```

* Power-On the ROCK64
* Wait until you see the OpenBSD Installer message:

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/openbsd_installer.png "OpenBSD Installer")

* Install OpenBSD: Follow the steps of the OpenBSD installer
* After successfull installation reboot OpenBSD

### Step 3 - Have fun with OpenBSD on the ROCK64!

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/openbsd_hello.png "OpenBSD Welcome")

**dmesg output after install:**

```
$ dmesg
OpenBSD 6.3-current (GENERIC.MP) #16: Thu Jun  7 13:04:56 MDT 2018
    deraadt@arm64.openbsd.org:/usr/src/sys/arch/arm64/compile/GENERIC.MP
real mem  = 4216815616 (4021MB)
avail mem = 4018982912 (3832MB)
mainbus0 at root: Pine64 Rock64
cpu0 at mainbus0 mpidr 0: ARM Cortex-A53 r0p4
efi0 at mainbus0: UEFI 2.0.5
efi0: Das U-boot rev 0x0
psci0 at mainbus0: PSCI 1.0
syscon0 at mainbus0: "syscon"
syscon1 at mainbus0: "power-management"
rkclock0 at mainbus0
syscon2 at mainbus0: "syscon-usb"
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
dwmmc0 at mainbus0: 48 MHz base clock
sdmmc0 at dwmmc0: 8-bit, mmc high-speed, dma
dwmmc1 at mainbus0: 48 MHz base clock
sdmmc1 at dwmmc1: 4-bit, sd high-speed, mmc high-speed, dma
dwge0 at mainbus0
dwge0: address: 12:a1:57:33:b4:3a
rgephy0 at dwge0 phy 0: RTL8169S/8110S/8211 PHY, rev. 6
ehci0 at mainbus0
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 configuration 1 interface 0 "Generic EHCI root hub" rev 2.00/1.00 addr 1
cpu1 at mainbus0 mpidr 1: ARM Cortex-A53 r0p4
cpu2 at mainbus0 mpidr 2: ARM Cortex-A53 r0p4
cpu3 at mainbus0 mpidr 3: ARM Cortex-A53 r0p4
scsibus0 at sdmmc0: 2 targets, initiator 0
sd0 at scsibus0 targ 1 lun 0: <SD/MMC, NCard, 0000> SCSI2 0/direct removable
sd0: 59000MB, 512 bytes/sector, 120832000 sectors
scsibus1 at sdmmc1: 2 targets, initiator 0
sd1 at scsibus1 targ 1 lun 0: <SD/MMC, SC32G, 0080> SCSI2 0/direct removable
sd1: 30436MB, 512 bytes/sector, 62333952 sectors
vscsi0 at root
scsibus2 at vscsi0: 256 targets
softraid0 at root
scsibus3 at softraid0: 256 targets
bootfile: sd0a:/bsd
boot device: sd0
root on sd1a (230f9d20b25da824.a) swap on sd1b dump on sd1b
$
```

##### Credits:

Thanks to Mark Kettenis from the OpenBSD ARM mailing-list.
