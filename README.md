debian-pi-netinst
===========
The minimal Raspbian over Network installer for Pi Model 1B, 1B+, 2B, 3B or the Zero.

This project provides a minimal base system install over network using latest Raspbian packages (_Jessie_)

The installer with default settings configures eth0 with DHCP to get Internet connectivity and completely wipes the SD card.

There are different kinds of "presets" that define the default packages that are going to be installed. Currently, the default one is called _minimal_ which installs only the essential base system packages including _NTP_ and _OpenSSH_ to provide a sane minimal base system that you can immediately after install ssh in and install your software. **_(no logging, no text editor, no cron)_**

Other presets include **_server_** which has more packages (logging, text editor, cron, ca-certificates) and **_base_** which doesn't even have networking. You can customize the installed packages by adding a small configuration file (_installer-config.txt_) to your SD card before booting up.

Features
--------
 - completely unattended/network, you only need working Internet connection through the Ethernet port
 - DHCP and static ip configuration _(DHCP is the default)_
 - always installs the latest version of Raspbian
 - configurable default settings
 - installation takes  **15 minutes** with fast Internet from power on to sshd running
 - fits in 1G SD card
 - default install includes fake-hwclock to save time on shutdown
 - default install includes NTP to keep time synced
 - /tmp is mounted as tmpfs to improve speed
 - no clutter included, you only get the bare essential packages
 - option to install root to USB drive

Requirements
------------
 - A Raspberry Pi Model 1B, Model 1B+, Model 2B, Model 3B or the Zero
 - SD card of at least 1GB (or at least 128MB for USB root install)
 - working Ethernet with Internet connectivity (Wifi can be used, instructions soon)

Raw Image
----------------
Prebuilt image is around **24MB**. It contains the same files as the .zip but is more convenient for users writing to SD Cards.

http://rpi.letstalkcoding.net/raspinimal.img

To use an image file, you will need to write it to a suitable 2GB or larger SD card using the UNIX tool **_[DD][2]_**. Windows users should use _**[Win32DiskImager][3]**_. Do not try to drag and drop the file to SD Card  without using dd or Win32DiskImager.... it won't work!!


Obtaining installer files on Windows and Mac
--------------------------------------------
Installer archive is around **24MB** and contains all firmware files and the installer.

http://rpi.letstalkcoding.net/raspinimal.zip

Format your SD card as **FAT32** (MS-DOS on _Mac OS X_) and extract the installer files in.

SD card image for Linux
-----------------------
Prebuilt image is around **20MB** xz compressed and **64MB** uncompressed. It contains the same files as the .zip but is more convenient for Linux users.

http://rpi.letstalkcoding.net/raspinimal.img.xz

To flash your SD card on Linux:

    xzcat /path/to/raspinimal.img.xz > /dev/sdX

Replace _/dev/sdX_ with the real path to your SD card.


Installing
----------
In normal circumstances, you can just power on your Pi and cross your fingers. Please edit custom configurations in a linux enviroment or use our pre-made configs

If you do have a display, you can follow the progress and catch any possible errors in the default configuration or your own modifications.

_**We do not offer support, however we are open to suggestions**_

First boot
----------
The system is almost completely unconfigured on first boot. Here are some tasks you most definitely want to do on first boot.

The default login is **_root_** and the password is **_raspbian_**.
  
  Change root password: `passwd`
  Configure your default locale: `dpkg-reconfigure locales`  
  Configure your timezone: `dpkg-reconfigure tzdata`  
  Install latest firmware package: `apt-get install libraspberrypi-bin libraspberrypi-dev libraspberrypi0` 

  _Installing the firmware package is **strongly** recommended. Please reboot after installing_

- Optional: `apt-get install raspi-copies-and-fills` for improved memory management performance.
- Optional: `apt-get install raspi-config` the tool for overclocking and much more.
- Optional: `echo "CONF_SWAPSIZE=128" > /etc/dphys-swapfile && apt-get install dphys-swapfile` 
- Creates a 128mb swap which is enabled on boot

Reinstalling or replacing an existing system
--------------------------------------------
If you want to reinstall with the same settings you did your first install you can just move the original _config.txt_ back and reboot. Make sure you still have **kernel-rpi1_install.img & kernel-rpi2_install.img** and **installer.cpio** also **defaultconfig.txt** in your _/boot_ partition.

    cp /boot/config-reinstall.txt /boot/config.txt
    reboot

**Remember to backup all your data and original config.txt before doing this!**

Installer customization
-----------------------
While defaults should work for most power users, some might want to customize default configuration or the package set even further. The installer provides support for this by reading a configuration file _installer-config.txt_ from the first vfat partition. The configuration file is read in as a shell script so you can abuse that fact if you so want to.

Easiest way to do this is to first _xzcat_ the image to your SD card and then mount the first partition to add your configuration file.

The format of the file and the current defaults:

    preset=minimal
    packages= # comma separated list of extra packages
    mirror=http://mirrordirector.raspbian.org/raspbian/
    release=wheezy
    hostname=pi
    rootpw=raspbian
    cdebootstrap_cmdline=
    bootsize=+64M # /boot partition size as given to fdisk
    rootsize=     # / partition size, leave empty to use all free space
    timeserver=time.nist.gov
    ip_addr=dhcp # Put static IP here 
    ip_netmask=0.0.0.0
    ip_broadcast=0.0.0.0
    ip_gateway=0.0.0.0
    ip_nameservers= # nameserver here
    online_config= # URL to extra config that will be executed after installer-config.txt
    usbroot= # set to 1 to install to first USB disk
    cmdline="dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 elevator=deadline"
    rootfstype=ext4
    rootfs_mkfs_options=

All of the configuration options should be clear. You can override any of these in your _installer-config.txt_. The time server is only used during installation and is for _rdate_ which doesn't support the NTP protocol.

Available presets: _minimal_, _server_ and _base_.

Presets set the `cdebootstrap_cmdline` variable. For example, the current _minimal_ default is:

> _--flavour=minimal --include=kmod,fake-hwclock,ifupdown,net-tools,isc-dhcp-client,ntp,openssh-server,dialog,locales,udev,apt-utils,wget_

There's also a post-install script support which is executed just before unmounting the filesystems. You can use it to tweak and finalize your automatic installation. The script should reside in the first vfat partition and have a name of _post-install.txt_.

Thanks & Credit
---------------
A big thanks to _[Toni Spets][4]_ for the original copy. 

Contact / Help
---------------

We now have a channel on _[LTC][7]_ for those who wish to come talk about this project. We do not want users coming in saying we stole the project ETC because you'll just be removed. This channel is Raspinimal only

**Server:** irc.letstlakcoding.net 6667 

**SSL: 6697**

**Channel:** #raspinimal

  [1]: http://www.raspbian.org/ "Raspbian"
  [2]: http://en.wikipedia.org/wiki/Dd_(Unix) "dd"
  [3]: http://sourceforge.net/projects/win32diskimager "Win32DiskImager"
  [4]: https://github.com/hifi "Toni Spets"
  [5]: http://rpi.letstalkcoding.net "RPi"
  [6]: https://github.com/hifi/raspbian-ua-netinst "raspbian-ua-netinst"
  [7]: http://www.letstalkcoding.net "LetsTalkCoding"
