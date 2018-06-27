# debian-mexn

installation of a Debian full-JavaScript VM server MEXN = (MongoDB, Express, [Vue, Angular, React, dhtmlx, AngularJS, jQuery, Ember, ...], Node)

# Getting ready

Assuming you have 

- [ ] the latest [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed and 
- [ ] the latest [Debian ISO file](https://www.debian.org/distrib/) - debian-9.4.0-amd64-netinst.iso (291MB) downloaded

Note to self: debian-9.4.0-i386-netinst.iso is wrong image, this is 32 bit

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
systemctl enable mongod.service
service mongod start
```

## Testing
```bash
shutdown -r now
root@debian:/home/dev# mongo
MongoDB shell version v3.6.5
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.5
Server has startup warnings:
2018-06-27T19:31:36.381+0200 I STORAGE  [initandlisten]
2018-06-27T19:31:36.381+0200 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2018-06-27T19:31:36.381+0200 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2018-06-27T19:31:38.066+0200 I CONTROL  [initandlisten]
2018-06-27T19:31:38.066+0200 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2018-06-27T19:31:38.066+0200 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2018-06-27T19:31:38.066+0200 I CONTROL  [initandlisten]
> show dbs;
admin   0.000GB
config  0.000GB
local   0.000GB
> use admin
switched to db admin
> show collections;
system.version
> db.system.version.find({})
{ "_id" : "featureCompatibilityVersion", "version" : "3.6" }
> exit
bye
```

# Install Node
```bash
apt-get install curl
curl -sL https://deb.nodesource.com/setup_9.x | bash -
apt-get install -y nodejs
```

## Testing
```bash
root@debian:/home/dev# node -v
v9.11.2
root@debian:/home/dev# npm -v
5.6.0
root@debian:/home/dev#
```

## Avoid EACCES errors

@see [here](https://docs.npmjs.com/getting-started/fixing-npm-permissions)

as dev

```bash
exit
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
source ~/.profile
```

Before we get something using npm, we need to install git as root

```bash
su
apt-get install git
exit
```

as dev

```bash
npm install -g jshint
npm uninstall -g jshint
```

# Full Test

as dev

```bash
git clone https://github.com/rkristelijn/dhtmlx-json-node.git
cd dhtmlx-json-node
npm i
npm run seed
CTRL+C
npm start
```

# Git server

`//todo` 
