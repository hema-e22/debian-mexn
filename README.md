# debian-mexn

installation of a Debian full-JavaScript VM server MEXN = (MongoDB, Express, [Vue, Angular, React, dhtmlx, AngularJS, jQuery, Ember, ...], Node)

# Getting ready

Assuming you have 

- [ ] the latest [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed and 
- [ ] the latest [Debian ISO file](https://www.debian.org/distrib/) - debian-9.4.0-i386-netinst.iso (377MB) downloaded

Note to self: i386 is wrong image, this is 32 bit

# Creating the image

Start VirtualBox, 
- create a new 64 bit debian (type=linux)
- I use 4 GB (4096)
- Create new virtual disk, 16 GB, MongoDB needs it
- I use VDI, dynamically allocated
- mount the iso file in settings on the CD-drive and boot the image

# Installing Debian

- Install type: Graphical Install
- Select language: English
- Location: other, Europe, Netherlands
- Locales: en_US.UTF-8
- Keymap: American English
- hostname: debian
- domainname: local
- root password: 
- Partition disk: guided, entire disk
- Separate /home /var /tmp
- Don't scan for CD's
- Debian mirror: Netherlands, ftp.nl.debian.org
- No proxy
- No stats send
- flagged:
  - Debian desktop environment: KDE
  - ssh server
  - standard utilities
- Select and install software
  - virtualbox-ose-guest-x11 (sweet how that autodetects)
- GRUB Bootloader to master boot record on /dev/sda

Done, after reboot I get a shell

- Check version
```bash
cat /etc/issue
Debian GNU/Linux 9 \n \l
```

- Install ifconfig, netstat etc
```bash
apt-get install net-tools
```

- List active ports (need root for p flag)

```bash
netstat -lptn
```

- Install sshd
```bash
apt-get install openssh-server
```

- Check IP address after changing network

```bash
ip address
```

Make sure you have host-only adapter

# Install MongoDB

Connect using putty, use dev, then su to root

use shift-insert to paste commands in putty

```bash
apt-get install dirmngr
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
echo "deb http://repo.mongodb.org/apt/debian stretch/mongodb-org/3.6 main" | tee /etc/apt/sources.list.d/mongodb-org-3.6.list
apt-get update
apt-get install -y mongodb-org
```
