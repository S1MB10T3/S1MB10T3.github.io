---
title: Unity
layout: project
logo: unity.png
image: 404.gif
---

A open source smart home powered by [Open Assistant](https://github.com/openassistant) running on a RaspberryPi!

# Setting Up

## Installing Raspbian
Follow the [official Raspbian installation guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

Once you have written Raspbian to your SD card you would want to edit some files before hand.
The best setup to work with your in my option is to get it connected to a keyboard, mice, and monitor. If you don't have access to those , you next best step for working on your pi is to remotely connect to it with another device such as your laptop with SSH.

## SSH

To be able to SSH to the RaspberryPi you first need to enable it. By creating a empty file named `ssh` to the boot partition of the SD card, SSH will now be enable. You can do this via the terminal by `touch ssh` in the `boot` partition.

### Connecting via Ethernet
Connecting your Pi with a Ethernet cord to your network is the easiest setup if you have access to one.

### Connecting via WiFi
If you don't have access to a spare monitor and keyboard, you next best step for
working on your pi is to remotely connect to it with another device such as your
laptop with SSH.

The RaspberryPi 2 doesn't have a built in WiFi adapter so you would need to use a
usb WiFi adapter to connect wirelessly.


##### Phone Hotspot
If your stuck with a odd wifi network you can use your phone's
hotspot so you can ssh from your laptop and your pi. This would be your best bet
to work on your Pi during class sessions.

First want to to setup an hotspot on your phone:

iPhone: [Hotspot Guide - macworld](https://www.macworld.co.uk/how-to/iphone/how-turn-iphone-into-wi-fi-hotspot-3513223/)

Android: [Hotspot Guide - androidauthority](https://www.androidauthority.com/mobile-hotspot-android-hotspot-android-customization-631280/)

Once you have your network work setup, follow the follow steps depending on your
OS on your computer.

#### Linux/Mac OS

Open up a terminal and enter:

```
sudo nano /media/[user]/[device]/etc/network/interfaces
```
_Note: To copy/paste on a terminal use `ctrl+shift+c`/`ctrl+shift+v`_

Once you have the file opened up on nano and enter the following into the editor:

```
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
auto wlan0


iface wlan0 inet dhcp
        wpa-ssid "[ssid]"
        wpa-psk "[password]"
```


Hit ctrl+x and then Y to exit and save the changes.

Once you have modified `interfaces` you can now unmount your sd card and reinsert it
back into the pi. No you can power up your Raspberry Pi (with a wireless network adapter)
and your Raspberry Pi should now be connected to the network.

### SSH

To SSH to your pi you need to have your computer connected to the same network as
your pi. Once you got that in check you need to find your Pi's ip address.

```
sudo nmap -sL 192.168.1.* | grep \(1
```

In most cases the pi of your pi will start with a 192.168.1.0/24 but if you ran
the command above and go no devices in return the wireless network might have formated
the ip differently.

Enter `ifconfig` or `ip a` into the terminal and you'll get a message like this in
return:

```
rantahu@meme-machine:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 84:3a:4b:0c:9b:84 brd ff:ff:ff:ff:ff:ff
    inet 192.168.43.250/24 brd 192.168.43.255 scope global dynamic wlp3s0
       valid_lft 3589sec preferred_lft 3589sec
    inet6 fe80::286c:e445:2dd0:817f/64 scope link
       valid_lft forever preferred_lft forever
```

Under wlp3s0 (the name of my wireless network card, might be different for your machine)
the inet is `192.168.43.250`, so More than linkly the pi ip starts with `192.169.43`

```
sudo nmap -sL 192.168.43.* | grep \(1
```

```
rantahu@meme-machine:~$ nmap -sL 192.168.43.* | grep \(1
Nmap scan report for raspberrypi (192.168.43.221)
Nmap scan report for meme-machine (192.168.43.250)
```

```
ssh pi@[pi's ip]
```

By default the username and password on Raspbian should `pi` and `raspberry`.

Once you enter your password you all set. Now you can start hacking.

# Hardware
For any smart home speaker you would need 1. a device to listen to your commands
ie. a microphone and 2. a device to play audio ie. speakers. There are a lot of
options when it comes to picking a microphone and speakers.

For my microphone I went with
[Kinbo's USB 2.0 Mini Microphone](https://www.amazon.com/gp/product/B00IR8R7WQ/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1)
and I can't recommend it to anyone for this project.

For my speakers I went with using [Adafruit Speaker Bonnet](https://www.adafruit.com/product/3346)
along with the [Adafruit Stereo Speaker](https://www.adafruit.com/product/1669).
This setup does require you to solder and set up from files in order for it to work.
To setup the speakers check [Adafruit's Guide](https://learn.adafruit.com/adafruit-speaker-bonnet-for-raspberry-pi/assembly).

You could use the headphone jack to output the Pi's audio. Keep in mind if you want
to connect speakers via this port you would need to power those speakers ethier through
the Pi's usb ports or another power source.

## Notes
I came across some issues getting my speaker bonnet to work the first time around.
I first check out my soldering to see if any connection wasn't working but that didn't seem to be an issue.

# Open Assistant
For this ai smart home brain I went with Open Assistant because it was the only
open source voice assistant that had a closed system, all data was process on the
machine rather then a cloud service. It was a pain to work with. But here's some
steps to install it so you don't run into the same issues like me.

## Installing Open Assistant
_I'm in the works of building a installation script for Open Assistant for the Pi. Stay tuned for that!_

### Update Python and Pip
Be sure Python and pip are updated for Python 3.5
```
sudo apt-get install python3 python3-pip pip3
```

### Install the pre alpha builds of sphinxbase and pocketsphinx
Sadly you need to compile these package as theirs only stable prebuilt versions of both packages.

On your terminal enter this to download both packages.

```
git clone https://github.com/cmusphinx/sphinxbase.git
git clone https://github.com/cmusphinx/pocketsphinx.git
```

### Install bison, ALSA, and swig
```
sudo apt-get install bison libasound2-dev swig
```

### Compile sphinxbase
```
cd sphinxbase
./configure --enable-fixed
make
sudo make install
cd ..
```

### Compile pocketsphinx
```
cd ../pocketsphinx
./configure --enable-fixed
make
sudo make install
cd ..
```

Now to test out installation with
```
src/programs/pocketsphinx_continuous -samprate 48000
```

If everything went smoothly with installation you should be able to speak and have
the that read back to you in text on your terminal.

_Note_
_On my second go on this project pocketsphinx started to fail. I believe it do to
 the speaker bonnet script preventing a microphone to be found on the pi. I don't have a fix yet... say tuned for that!_

### Installing GStreamer
For installing GStreamer and it's plugins I recommend using [this script](https://gist.github.com/sqwk/096d789f789335019756) written by Sqwk
You can run it by ether this into your terminal.

```
bash <(curl -s https://gist.github.com/sqwk/096d789f789335019756)
```

### Installing Request
```
pip3 install requests
```

### Install PyGOhject
```
sudo apt install python-gi python-gi-cairo python3-gi python3-gi-cairo gir1.2-gtk-3.0
```

## Downloading and Running Up Open Assistant
```
git clone https://github.com/openassistant/oa-core.git
cd oa-core
chmod +x ./run.sh
./run.sh -c -H20 -m0 -M mind/boot
```

Now if everything is running smoothly say Launch Open Assistant to enable operating
system controls like volume control and Launch Stella to enter dialogue mode.

This is guide should in theory get you started on working with your own smart home
speaker. But like with everything computer it's not going to work out of the box
with these instructions seeing that I still have issues currently with my microphone.
But I have gotten pocketsphinx to properly install and was able to test it out with
my microphone.
