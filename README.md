# debian-mexn

installation of a Debian full-JavaScript VM server MEXN = (MongoDB, Express, [Vue, Angular, React, dhtmlx, AngularJS, jQuery, Ember, ...], Node)

# Getting ready

Assuming you have 

- [ ] the latest [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed and 
- [ ] the latest [Debian ISO file](https://www.debian.org/distrib/) - debian-9.4.0-i386-netinst.iso (377MB) downloaded

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

Done
