# debian-mexn
installation of a Debian full-JavaScript VM server

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



