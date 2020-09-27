---
description: Raspberry Pi - Tor-Pi how to make Raspberry Pi as Wifi Tor Access Point Guide
---

# TorPi - Raspberry Pi Tor Access Point

## Network Flow

<div style="margin:0 auto">
   <img src="/assets/images/RaspberryPi/torPi/networkDiagram.png" alt="Tor-Pi Network Flow">
</div>

## Installing Raspberry Pi OS Minimal Headless

Download **Raspberry Pi OS Minimal** image from: [raspberrypi.org](https://www.raspberrypi.org/downloads/raspberry-pi-os/ 'Raspberry Pi OS Download')
It's a headless os - **Without GUI**

<div style="width:80%; margin:0 auto">
   <img src="/assets/images/RaspberryPi/torPi/rasberryPiOsMinimal.png" alt="TorPi">
</div>

Burn **Raspberry Pi OS Minimal** image to SD-Card that will be used in this project for TorPi
Since we don't won't to use external screen or keyboard, we need to allow an SSH access to the Raspberry Pi OS on the first boot.

After we created our bootable SD card we need to mount it and add a file called **“ssh”** inside a boot partition.
This will enable and start ssh daemon on pi at boot.

<div style="width:40%; margin:0 auto">
   <img src="/assets/images/RaspberryPi/torPi/ssh_file.png" alt="TorPi">
</div>

To continue the setup we will need a Ethernet Cable with DHCP and Internet Connection.
Insert the SD card and the Ethernet cable and boot your pi by connecting power.
At this point the pi should boot the new OS from the SD card and get a DHCP address.
Find the new address your pi just got from your dhcp server. You can do it inside your router's ui or use nmap tp find it on the network. If you can't find the new address you can allows connect it to external screen and keyboard - use default credentials to login and 'ip addr' command

SSH to the Raspberry Pi
Default credentials:

-   User: **pi**
-   Password: **raspberry**

```bash
ssh pi@192.168.1.111
```

Change the default password for the Pi user

```bash
passwd
```

Optional: [SSH Hardening with RSA Keys](https://3os.org/linux/SSH_Service_Security/#ssh_service_security 'SSH Hardening with RSA Keys')

Let's run system updates

sudo apt update && sudo apt full-upgrade -y

---

```bash
sudo apt-get update && sudo apt-get upgrade && sudo apt-get install git
sudo raspi-config
```

Change User Password within the Config Interface
Enable SSH In Interface Options

```bash
sudo reboot
```

## Wifi Hotspot Configuration

SSH TO Raspberry Pi 3

```bash
ssh pi@raspberry_host
git clone https://github.com/unixabg/RPI-Wireless-Hotspot.git
cd RPI-Wireless-Hotspot
sudo ./install
```

-   "Y" to agree to terms
-   "Y" to use preconfigured DNS
-   "Y" to use Unblock-Us DNS servers
-   "N" for WiFi defaults
-   Type in a new WiFi password (it will be checked)
-   Type in a new SSID
-   Type in your desired WiFi channel (1, 6, 11)
-   Type "N" when asked - "Are you using a rtl871x chipset?"
-   Type "N" for chromecast support (unless you plan to use a chromecast w/RasTor)

if you get "Failed to start hostapd.service: Unit hostapd.service is masked." error when installing the script, execute this:

```bash
sudo systemctl unmask hostapd
sudo systemctl enable hostapd
sudo systemctl start hostapd
```

reboot before testing thw wifi

## Testing Wifi Hotspot

Connect to new SSID with a Phone and check if you have full Internet Connection.

## Installing Tor Service

```bash
sudo apt-get install tor
sudo nano /etc/tor/torrc
```

Add the following just below the first set of comments:

```bash
Log notice file /var/log/tor/notices.log
VirtualAddrNetwork 10.192.0.0/10
AutomapHostsSuffixes .onion,.exit
AutomapHostsOnResolve 1
TransPort 192.168.42.1:9040
TransListenAddress 192.168.42.1
DNSPort 192.168.42.1:53
DNSListenAddress 192.168.42.1
```

## Configure iptables

```bash
sudo iptables -F && sudo iptables -t nat -F
sudo iptables -t nat -A PREROUTING -i wlan0 -p udp --dport 53 -j REDIRECT --to-ports 53
sudo iptables -t nat -A PREROUTING -i wlan0 -p tcp --syn -j REDIRECT --to-ports 9040
```

Check iptables routes:

```bash
sudo iptables -t nat -L
```

if all routs looks like about run this command:

```bash
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```

## Create log file

```bash
sudo touch /var/log/tor/notices.log
sudo chown debian-tor /var/log/tor/notices.log && sudo chmod 644 /var/log/tor/notices.log
```

## Testing and finishes

Start TOR service:

```bash
sudo service tor start
```

Check to see if the service is running:

```bash
sudo service tor status
```

Run TOR Service at Boot:

```bash
sudo update-rc.d tor enable
sudo reboot
```

Test for TOR service is running after reboot, connect to the TOR WIFI.

## Optional: Install Monit Service to reload Tor Serivce if Down

```bash
sudo apt-get install monit
sudo nano /etc/monit/monitrc
```

Add those lines to the end of the config:

```bash
check process gdm with pidfile /var/run/tor/tor.pid
   start program = "/etc/init.d/tor start"
   stop program = "/etc/init.d/tor stop"
```

Reload and add Monit to startup:

```bash
systemctl restart monit
systemctl enable monit
```

## Bonus

Chnage Exit Ip every 10 sec
add this to the end of /etc/tor/torrc

```bash
CircuitBuildTimeout 10
LearnCircuitBuildTimeout 0
MaxCircuitDirtiness 10
```