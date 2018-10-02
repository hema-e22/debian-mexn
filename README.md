# debian-mexn

installation of a Debian full-JavaScript VM server MEXN = (MongoDB, Express, [Vue, Angular, React, dhtmlx, AngularJS, jQuery, Ember, ...], Node)

# Getting ready

Assuming you have 

- [ ] the latest [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed and 
- [ ] the latest [Debian ISO file](https://www.debian.org/distrib/) - debian-9.4.0-amd64-netinst.iso (291MB) downloaded

Note to self: debian-9.4.0-i386-netinst.iso is wrong image, this is 32 bit

If you need Debian on a physical machine, easiest is to create a [bootable usb-stick](https://unix.stackexchange.com/questions/263615/how-to-create-a-bootable-debian-usb-drive-using-windows):

## STEP 1

Go to the website of the OS you wish to install, and find an iso image to download. In your case, since you want to run a Debian OS, here is a link to its iso options: [https://www.debian.org/distrib/netinst](https://www.debian.org/distrib/netinst)

Choose an iso image from the options, and click on it. This should automatically start the image download. While file is downloading, go to second step.

## STEP 2

Get a utility program to format and create bootable USB flash drives. Some have already been suggested, so I will just link you to my favourite: [https://rufus.akeo.ie/](https://rufus.akeo.ie/)

Download the utility and go to third step.

## STEP 3

By this stage, if your iso image has not yet finished downloading, then wait until it does.

Now that you have both the utility and the iso image downloaded:

1. Plug in your USB drive
1. Open Rufus (to write your USB)
1. Select the iso image you just downloaded to write on the USB, and fill out the other options accordingly (eg. selecting your USB drive etc)
1. Click on the option for starting the write process (with Rufus, it is the "Start" button)
Once Rufus finishes, simply reboot, booting from your USB, which should start up your Debian OS.

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

# Install VirtualBox Guest Additions
[@see](https://www.linuxbabe.com/desktop-linux/how-to-install-virtualbox-guest-additions-on-debian-step-by-step)

## Connect remotely from Linux with multi-monitor set-up
0. You need to have VirtualBox Guest Additions installed on your client machine
1. You need to install Oracle VM VirtualBox Extension Pack
2. You need to enable remote desktop in Oracle VM VirtualBox Manager for the client
3. You need to start the guest headless
3. You need to install xfreerdp 

`apt-get install freerdp`

Connect using:

`xfreerdp /v:192.168.178.xxx:3389 -f /multimon`

CTRL+ALT+ENTER to escape fullscreen

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

`apt-get install git-core`

# Add user to sudo
add you user below root in `/etc/sudousers`

and without password

`my-awesome-user ALL=(ALL) NOPASSWD:ALL`

# Graphical tools

If you had X11 installed; here's my tips for LXDE

## Add shortcuts:
edit `~/.config/openbox/lxde-rc.xml`

replace USER with your user

`sudo code ~/.config/openbox/lxde-rc.xml --user-data-dir USER`

disable `<!-- Keybindings for desktop switching -->`
### Use Windows + (Left|Right) to move windows to different monitors
```xml
<keybind key="W-Right">
      <action name="MoveResizeTo">
        <!-- move to next monitor -->
        <monitor>next</monitor>
      </action>
    </keybind>
    <keybind key="W-Left">
      <action name="MoveResizeTo">
        <!-- move to next monitor -->
        <monitor>prev</monitor>
      </action>
    </keybind>
```
### Use Ctrl + Alt + (1|2|3|4) to move windows a quarter of the screen
```xml
<keybind key="C-A-1">        # UpperQuarterLeft
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>0</x><y>0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-2">        # LowerQuarterLeft
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>0</x><y>-0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-3">        # LowerQuarterRight
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>-0</x><y>-0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-4">        # UpperQuarterRight
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>-0</x><y>0</y><height>50%</height><width>50%</width></action>
    </keybind>
```

### Use Ctrl + Alt + (Left|Right) to move windows a left or right half
```xml
<keybind key="C-A-1">        # UpperQuarterLeft
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>0</x><y>0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-2">        # LowerQuarterLeft
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>0</x><y>-0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-3">        # LowerQuarterRight
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>-0</x><y>-0</y><height>50%</height><width>50%</width></action>
    </keybind>

    <keybind key="C-A-4">        # UpperQuarterRight
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>-0</x><y>0</y><height>50%</height><width>50%</width></action>
    </keybind>
```

### Use Ctrl + Alt + (Up|Down) to move windows fullscreen and restore
```xml
    <keybind key="C-A-Up">        # FullScreen
      <action name="Maximize"/>
    </keybind>
    <keybind key="C-A-Down">        # MiddleScreen
      <action name="UnmaximizeFull"/>
      <action name="MoveResizeTo"><x>center</x><y>center</y><width>50%</width><height>50%</height></action>
    </keybind>
```

### Miscelious Programs
```xml
<keybind key='W-t'>
            <action name='Execute'>
                <command>lxterminal</command>
            </action>
        </keybind>
        <keybind key='C-A-t'>
            <action name='Execute'>
                <command>lxterminal</command>
            </action>
        </keybind>
        <keybind key='C-A-i'>
            <action name='Execute'>
                <command>/usr/bin/google-chrome-stable %U</command>
            </action>
        </keybind>
        <keybind key='W-i'>
            <action name='Execute'>
                <command>/usr/bin/google-chrome-stable %U</command>
            </action>
        </keybind>
<keybind key='C-A-l'>
            <action name='Execute'>
                <command>leafpad %f</command>
            </action>
        </keybind>
        <keybind key='W-l'>
            <action name='Execute'>
                <command>leafpad %f</command>
            </action>
        </keybind>
```

# Install Visual Studio code
`sudo dpkg -i code_1.27.2-1536736588_amd64.deb`

# Install Chrome
`sudo dpkg -i google-chrome-stable_current_amd64.deb` 


# Install Postman
https://www.getpostman.com/apps
`tar -zxvf Postman-linux-x64-6.3.0.tar.gz`
mv Postman /usr/bin

# Install Nomachine

We don't need this, as we connect to the machine via VirtualBox
https://www.nomachine.com/download

