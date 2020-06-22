---
description: Linux General how to, guides, examples, and simple usage
---

# Linux General Topics

## Fix Locales (Fix Bash Local Error)

Generate en_US.UTF-8 locale

```bash
locale-gen "en_US.UTF-8"
```

Set the Locale, Find the en_US.UTF-8 in the list and select it, at the following screen select it.

```bash
dpkg-reconfigure locales
```

## Set System Time With Time Zone (timedatectl ntp)

Find your time zone with __timedatectl list-timezones__ use __grep__ for easier results:

```bash
timedatectl list-timezones | grep "Toronto"
```

The output should look like this:

```bash
America/Toronto
```

Now set the Time Zone and active it.

```bash
timedatectl set-timezone America/Toronto
timedatectl set-ntp true
```

Now test timedatectl status

```bash
timedatectl status
```

Check your system time

```bash
date
```

## Service Status/Start/Stop/Enabling On Boot

```bash
systemctl status httpd
systemctl start httpd
systemctl stop httpd
systemctl enable httpd
```

## Run Script On Boot - Crontab

Only add sudo if your script needs superuser privileges. Then add this to your crontab:

```bash
(sudo) crontab -e
```

Add to the end of the Cront tab:

```bash
@reboot /path/to/script.sh
```

## Redirect Output to a File and Stdout With _tee_

The command you want is named `tee`:

```bash
foo | tee output.file
```

For example, if you only care about stdout:

```bash
ls -a | tee output.file
```

If you want to include stderr, do:

```bash
program [arguments...] 2>&1 | tee outfile
```

2>&1 redirects channel 2 (stderr/standard error) into channel 1 (stdout/standard output), such that both is written as stdout. It is also directed to the given output file as of the tee command.

Furthermore, if you want to append to the log file, use tee -a as:

```bash
program [arguments...] 2>&1 | tee -a outfile
```

## Add Permanent Path to Application

First find the location of the Application/Service:

```bash
find / -name ApplicationName
```

Go to the path where the application is located

```bash
cd "../../../ApplicationName"
```

Run this command for ZSH:

```bash
echo 'export PATH="'$(pwd)':$PATH"' >> ~/.zshrc && source ~/.zshrc
```

Run this command for "Bash Profile":

```bash
echo 'export PATH="'$(pwd)':$PATH"' >> ~/.profile && source ~/.profile
```

Run this command for "Bash":

```bash
echo 'export PATH="'$(pwd)':$PATH"' >> ~/.bashrc && source ~/.bashrc
```

## Create Symbolic Links

To create a symbolic link in Unix/Linux, at the terminal prompt, enter:

```bash
ln -s source_file target_file
```

to remove symbolic link use the `rm` command on the link

## Disable IPv6

For current session:

```bash
echo 1 > /proc/sys/net/ipv6/conf/<interface-name>/disable_ipv6
echo 1 > /proc/sys/net/ipv6/conf/eth0/disable_ipv6
```

Permanent:

edit /etc/sysctl.conf and add to the end of the config

```bash
net.ipv6.conf.all.disable_ipv6 = 1
```

Save. Then run:

```bash
sudo sysctl -p /etc/sysctl.conf
```

## Find PTR Owner - Reversal Look Up

```bash
dig 0.168.192.in-addr.arpa. NS
```

## Open Last Edited File

```bash
less `ls -dx1tr /usr/local/cpanel/logs/cpbackup/*|tail -1`
```

## Kill Process That Runs More Than X Time

Kill cgi after 30 secs:

```bash
for i in `ps -eo pid,etime,cmd|grep cgi|awk '$2 > "00:30" {print $1}'`; do kill $i; done
```
