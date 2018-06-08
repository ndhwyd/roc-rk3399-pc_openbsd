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

### Step 1 - Flash U-Boot in SPI-EEPROM from the ROCK64

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
* Connect the USB-RS232-TTL converter with the ROCK64
* Start minicom with the baud rate 1500000

```
$ minicom -8 -D /dev/ttyUSB0 -b 1500000
```

* Power-On the ROCK64
* Wait unil you see following:

![alt text](https://github.com/krjdev/rock64_openbsd/raw/master/images/flash.png "Flash")

### Step 2 - Install OpenBSD

* Download the file "miniroot63.fs" form the OpenBSD's mirrors

Link: https://ftp2.eu.openbsd.org/pub/OpenBSD/snapshots/arm64/ (Example link)

* Open the terminal
* Type the following (as example - change the command where you have downloaded the file minirooot63.fs and the correct device for the microSD card):

```
$ dd if=miniroot63.fs of=/dev/sde
```

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

##### Credits:

Thanks to Mark Kettenis from the OpenBSD ARM mailing-list.
