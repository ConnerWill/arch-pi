<div align="center">

<img width="800" height="650" src="arch-pi.png">



# arch-pi

### **RaspberryPi Arch Linux Installer**

> A simple script that installs [Arch Linux](https://www.archlinux.org/) on the [Raspberry Pi](https://www.raspberrypi.org/products/).

![GitHub last commit](https://img.shields.io/github/last-commit/ConnerWill/arch-pi)
![GitHub issues](https://img.shields.io/github/issues-raw/ConnerWill/arch-pi)
![GitHub repo size](https://img.shields.io/github/repo-size/ConnerWill/arch-pi)
[![GitLab](https://img.shields.io/static/v1?label=gitlab&logo=gitlab&color=E24329&message=mirrored)](https://gitlab.com/ConnerWill/arch-pi)
![GitHub](https://img.shields.io/github/license/ConnerWill/arch-pi)
![GitHub Repo stars](https://img.shields.io/github/stars/ConnerWill/arch-pi?style=social)
  
---

</div>

<!---

# Features

<details>
  <summary>Expand Features</summary>

 [✔] Comming Soon ...

 [✔] Script is fully functional ...

 [✔] Just working of documentation ...

 [✔] Comming Soon ...
  
 [__] BOTTOM TEXT
  
</details>

---

# Table of Contents

<details>
  <summary>Expand Table of Contents</summary>

* Comming Soon ...
  
</details>

--->


# How To Use

The installation procedure is very simular to the [Installation Guides from
Arch Linux ARM](http://archlinuxarm.org/).

The Fancy part about this script is it will automate the entire process, including adding some configuration settings to get the arch-pi setup to be a fully headless server such as setting static IP, and configuring SSH.

After the installation, you can directly login to your arch-pi using the pre-configured IP address.

## Requirements

To setup a RaspberryPi running Arch Linux, **arch-pi** requires a Linux environment that has internet access and has a SD card slot / reader *(Mac support is not yet implemented ...)*. 

If you do not have a Linux environment, you could temporarily boot a Live-CD Linux distribution like [Xubuntu](http://xubuntu.org/).

Just be sure the following commands are available:

> `lsblk` `dd` `parted` `curl` `tar`

The script supports the following hardware models of the
Raspberry Pi:
> * Raspberry Pi Model A/A+/B/B+, Compute Module, Zero, Zero W *(ARMv6)*
> * Raspberry Pi 2 Model B *(ARMv7)*
> * Raspberry Pi 3 Model B/B+ *(ARMv8, but using ARMv7)*
> * Raspberry Pi 4 Model B *(ARMv8, but using ARMv7)*

***NOTE:** For the time being, the Raspberry Pi 3 Model B will install the ARMv7
version of Arch Linux also used by the Raspberry Pi 2 Model B.*
## Usage Guide

Clone this repository 

```Bash
 git clone https://github.com/ConnerWill/arch-pi.git
```

Insert the SD card/USB which you want to install Arch Linux on, but make sure none
of its partitions are mounted, otherwise unmount them. 
Use the command `lsblk` to determine the device name, e.g. `/dev/mmcblk0`. 
Then open the configuration file in a text editor.

```Bash
"$EDITOR" "arch-pi-master/arch-pi.conf"
```

## Configuration
### Configuration Variables

---

**[[[VERY IMPORTANT!]]]**


**MAKE SURE** the `INSTALL_DEVICE` variable matches the device you wish to use. 

**Otherwise, if the wrong device is used, you can lose all of your data on your computer.**

---


You may also want to change the following settings:



`HOSTNAME`
: Hostname to set for the RaspberryPi  ***[string]***

`TIMEZONE`
: Local timezone ***[timezone]***

`CONSOLE_KEYMAP`
: Prefered keymap  ***[keymap]***

`SET_ETHERNET` 
: Decide if to use DHCP or configure a static IP. If `SET_ETHERNET` is 'yes' then the other 'ethernet' fields are required. *(Same applies for the wireless configuration)*  ***[yes|no]***

<details>
  <summary>Expand More Network Configuration Settings</summary>

```INI
## Ethernet Networking Configuration
SET_ETHERNET="yes" 
ETHERNET_INTERFACE="eth0"
ETHERNET_DHCP="no"
ETHERNET_ADDRESS="192.168.1.10/24"
ETHERNET_GATEWAY="192.168.1.1"
ETHERNET_DNS="192.168.1.1"
  
# If 'SET_WIRELESS' is 'yes' then the other wireless variables are required.
SET_WIRELESS="no"
WIRELESS_INTERFACE="wlan0"
WIRELESS_DHCP="no"
WIRELESS_ADDRESS="192.168.1.11/24"
WIRELESS_GATEWAY="192.168.1.1"
WIRELESS_DNS="192.168.1.1"
WIRELESS_SECURITY="wpa" # obsolete now - we support wpa(2) only
WIRELESS_ESSID=""
WIRELESS_KEY=""
WIRELESS_HIDDEN="no"
WIRELESS_COUNTRY="US"

### Disable IPv6
DISABLE_IPV6="yes"  
  
```

</details>


*The remainging configuration variables and brief descriptions of them can be found in* **[arch-pi.conf](arch-pi.conf)***.*

**Make sure to review the configuration file before running script.**

---


Once you are done, save and close the configuration file.

To write and format partitions on the SD card,
`arch-pi`
needs super-user privileges. So `su` to `root` or use `sudo` to start the
installation process:

```Bash
sudo arch-pi-master/arch-pi.sh
```

**CAUTION:** The installation will delete *all* existing data on the SD card.

The installation is done, once you see

```
[arch-pi] Wake up, Neo... The installation is done!
```

Then insert the SD card into your
Raspberry Pi
and start it up.

That's it!

You can login as the default user `alarm` with the password `alarm`.
The default root password is `root`.

### Initialize Pacman

Before you can install additional packages, you must initialize the pacman
keyring and populate the Arch Linux ARM package signing keys.

Login as `root` and type in:

```Bash
pacman-key --init && pacman-key --populate archlinuxarm
```

After Pacman is initialized, it's probably a good idea to check for available
package updates:

```Bash
pacman -Syyu
```

That's it!

### Installing Yay or Yaourt

`arch-pi`
can also download the packages required for installing
[Yay](https://github.com/Jguer/yay) or
[Yaourt](https://github.com/archlinuxfr/yaourt), by changing the `DOWNLOAD_YAY`
or `DOWNLOAD_YAOURT` settings. Both Yay and Yaourt in turn allow you to install
packages from the [AUR](https://aur.archlinux.org/).

**NOTE:** Yaourt is not maintained anymore.

Before you can install Yay or Yaourt, you first have to set up a build
environment, so login as `root` (password is `root`) and type in:

```Bash
pacman -Syy --noconfirm --needed base-devel sudo
```

Next, configure `sudo`, allowing members of the group `wheel` to use it by
editing the `sudoers` file:

```Bash
nano -w /etc/sudoers
```

Remove the leading `#` from the following line to uncomment it:

```Bash
# %wheel ALL=(ALL) ALL
```

Save the `sudoers` file by pressing `Ctrl-X`, `y`, `Enter` and then logout:

```Bash
logout
```

Login again, but this time as the user `alarm` (password is `alarm`), and change
to the directory containing the Yaourt packages:

```Bash
cd /home/alarm/software/aaa.dist
```

**NOTE:** The Yay and Yaourt packages are in `/home/alarm/software/aaa.dist`
unless you changed the `YAY_PATH` or `YAOURT_PATH` settings.

To install Yay:

```Bash
tar xvf yay.tar.gz
cd yay
makepkg -i -s --noconfirm --needed

cd ..
```

To install Yaourt:

```Bash
tar xvf package-query.tar.gz
cd package-query
makepkg -i -s --noconfirm --needed

cd ..

tar xvf yaourt.tar.gz
cd yaourt
makepkg -i -s --noconfirm --needed

cd ..
```

After Yay or Yaourt is installed, you can check for available package updates:

Using Yay:

```Bash
yay -Syyu
```

Using Yaourt:

```Bash
yaourt -Syyua
```

If there are, just follow the instructions on the screen.

That's it!

### Using an Alternative Configuration File

You can use an alternative configuration file by passing it to the installation
script:

```Bash
arch-pi-master/arch-pi.sh -c my.conf
```

## License

This software is distributed under the terms of the
[GNU General Public License v3](https://www.gnu.org/licenses/gpl-3.0.en.html).
