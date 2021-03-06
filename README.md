## [ quantifiedArt ] JS

---

### Introduction

This repository holds a reimplementation of the QuantifiedArt project developed at the [__Bosch__ _Internet of Things Lab_](http://iot-lab.ch) at University of St. Gallen and ETH Zürich.

We aim to provide a self installable, understandable solution that everyone with the right hardware can build for him- or herself.

The used software aims to be run on Single Board Computers (SBC) such as the Raspberry Pi and is built to be as minimal in processing demands as possible. The architecture is tested on a Raspberry Pi B+ Model 1.2 and is expected to work on any revision of this SBC (RPi 2 / 3 / zero).

For now the code supports DHT-type temperature and humidity sensors and works with K30 carbon dioxide sensor modules from SenseAir. We aim to provide support for other air parameter sensors in the future to keep this project being adapted by more and more people.

---

### Bill Of Materials

* 1 x Raspberry Pi B+ 1.2
* 1 x MicroSD-Card >= 8.0GB
* 1 x DHT-22 (AM2301) temperature and humidity sensor
* 1 x SenseAir k30 carbon dioxide sensor module
* 7 x Dupont wire jumpers
* 1 x Micro-USB cable
* 1 x HDMI display cable



### Wiring:

Connect the serial port of the k30 carbon dioxide sensor with the serial port of the raspberry pi:

#### k30 CO2 sensor:

pin RPi | function RPi | pin k30 | function k30
------- | ------------ | ------- | ------------
4 | vcc | 33 | vcc
6 | gnd | 32 | gnd
8 | tx | 34 | rx
10 | rx | 35 | tx

#### dht temperature & humidity sensor:

pin RPi | function RPi | color dht | function dht
------- | ------------ | --------- | ------------
2 | vcc | red | vcc
14 | gnd | black | gnd
12 | gpio 18 | yellow | data

---

### Installation:

#### The easy way:

Transfer the image to the MicroSD-Card and put it in the corresponding slot in the Raspberry Pi.

#### Manual installation:

Download a Raspbian distribution of your choice. If you are working with a Raspberry Pi 3 I highly recommend PIXEL as it comes with the needed window manager and chromium browser installed: https://www.raspberrypi.org/downloads/raspbian/

#### Disable console on serial port:

`sudo raspi-config`

--> Expand Filesystem

--> Advanced Options --> Serial --> < No >

`dmesg | grep tty`

If `ttyAMA0` does not show up, re-enable serial port:

`sudo nano /boot/config.txt`

Add, change or uncomment the line:
`enable_uart=1`

To enable full screen output add or uncomment the line:
`disable_overscan=1`

If you are working with a Raspberry Pi 3 add the following line to disable bluetooth:
`dtoverlay=pi3-disable-bt`

#### Update and upgrade system packages (could take a while...):

`sudo apt-get update && sudo apt-get upgrade -y`

`sudo apt-get dist-upgrade`

`sudo apt-get clean`

#### Install LXDE (skip this part is you downloaded PIXEL):

`sudo apt-get install --no-install-recommends xserver-xorg -y`

`sudo apt-get install --no-install-recommends xinit -y`

`sudo apt-get install lxde-core lxappearance -y`

`sudo apt-get install lightdm -y`

#### Install chromium-browser (skip this part is you downloaded PIXEL):

`sudo apt-get install chromium-browser -y`

#### Install nodejs and npm:

`sudo apt-get install nodejs npm -y`

`sudo npm install -g n`

`sudo n 6.9.1`

`sudo npm install -g npm@latest`

#### Install git and clone git repository:

`sudo apt-get install git -y`

`git clone https://github.com/ameeuw/qaJS`

#### Disable Screensaver:


`./qaJS/install/disableScreensaver.sh`

`< yes >` ... `< yes >`

### Option 1: user python server
#### Install python dependencies:

We are using the [Adafruit_DHT](https://github.com/adafruit/Adafruit_Python_DHT) Library to read out the DHT family of temperature and humidity sensors. To install the library use `pip install Adafruit_DHT` or install manually as described in the Adafruit git repository.

For communication with the CO<sub>2</sub> sensor, we are using python serial. Use `pip install serial` if not installed (comes with PIXEL).

### Option 2: use nodejs server
#### Install nodejs dependences:

Additional infos:
* [node-dht-sensor](https://github.com/momenso/node-dht-sensor)

* [serialport](https://github.com/EmergingTechnologyAdvisors/node-serialport)

##### Install dependency _bcm2835_:
`cd install`
`tar zxvf bcm2835-1.xx.tar.gz`
`cd bcm2835-1.xx`
`./configure`
`make`
`sudo make check`
`sudo make install`

##### Install node packages:
`cd node`
`npm install node-dht-sensor`
`npm install serialport`

#### Change splashscreen (For Raspbian PIXEL):
`/usr/share/plymouth/themes/pix/splash.png`

#### Create autostart entry at login:

`sudo nano ~/.config/lxsession/LXDE/autostart`

With Raspbian PIXEL:

`sudo nano ~/.config/lxsession/LXDE-pi/autostart`

Add line:

`@/home/pi/qaJS/launch.sh pyserver`

or

`@/home/pi/qaJS/launch.sh nodeserver`

depending on your preferences.

To hide the cursor:
`sudo apt-get install unclutter`
`@unclutter -idle 0.1 -root`

#### Make raspberry pi read-only

Follow [this guide](https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=161416).

### Start

If you are launching from an SSH session, make sure to set the $DISPLAY variable:
`export DISPLAY=:0`

`./launch.sh`
