---
description: Ubuntu & Debian Linux - Guides, Examples and Usage
---

# Ubuntu & Debian Related Topics

## Disable Firewall Ubuntu

```bash
ufw disable
```

## Debian/Ubuntu/RaspberryPiOS- Update Script

Is a simple bash script to run full full-upgrade update and cleanup
Basicity it will run those commands with pretty colors =)

-   sudo apt update
-   sudo apt full-upgrade
-   sudo apt autoclean
-   sudo apt autoremove

## Installation

```bash
wget https://raw.githubusercontent.com/fire1ce/debianUpdate/master/debianUpdate.sh
sudo chmod +x debianUpdate.sh
sudo mv debianUpdate.sh /usr/bin/update
```

## Run

```bash
update
```

### Install as Schedule at Crontab

```bash
crontab -e
```

Empale: this will run the script every day at 04:00

```bash
0 4 * * * /usr/bin/autoupdate
```

## Disable IPv6 Persistent

Disable IPv6 using GRUB
Perform the following steps with root privileges to disable IPv6 in Ubuntu 18.04/16.04 Permanently using grub method.

Edit:

```bash
/etc/default/grub
```

Modify _GRUB_CMDLINE_LINUX_ and _GRUB_CMDLINE_LINUX_DEFAULT_ to append ipv6.disable=1:

```bash
GRUB_CMDLINE_LINUX="ipv6.disable=1"
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"
```

Update the grub configuration:

```bash
update-grub
```

Reboot the server

## Unattended Upgrades

```bash
apt install -y unattended-upgrades apt-listchanges
```

Edit the config to your preference

```bash
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Example values in Debian:

```config
        "origin=Debian,codename=${distro_codename}-updates";
        "origin=Debian,codename=${distro_codename}-proposed-updates";
        "origin=Debian,codename=${distro_codename},label=Debian";
        "origin=Debian,codename=${distro_codename},label=Debian-Security";
        "origin=Debian,codename=${distro_codename}-security,label=Debian-Security";

Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::MinimalSteps "true";
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-WithUsers "true";
Unattended-Upgrade::Automatic-Reboot-Time "05:00";
```

Automatic call via /etc/apt/apt.conf.d/20auto-upgrades

```bash
echo unattended-upgrades unattended-upgrades/enable_auto_updates boolean true | debconf-set-selections
dpkg-reconfigure -f noninteractive unattended-upgrades
```

Check the /etc/apt/apt.conf.d/20auto-upgrades for those 2 lines:

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

Manual Run:

```bash
unattended-upgrade -d
```

To enable unattended-upgrade use the following command:

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

## Clear BOOT Partition on Ubuntu when 100%

```bash
dpkg --purge `dpkg --list|grep "linux-"|grep -v \`uname -r|sed 's/-generic//g'\`|cut -d" " -f3|grep "[0-9]-"|paste -sd " " -`
```
