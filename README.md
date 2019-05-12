
# Setting up Homebridge

* Modified from https://github.com/nfarina/homebridge/wiki/Easy-Install-Raspberry-PI-(With-Start-with-boot)
* Downloaded Raspbian Lite https://www.raspberrypi.org/downloads/raspbian/
* Install https://github.com/dubocr/homebridge-gpio-device#readme
  * NOTE --unsafe-perm is needed to not error out with permission denied.

## Installing on Rasberry Zero

From Mac:

```bash 
diskutil list
diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=Downloads/2019-04-08-raspbian-stretch-lite.img of=/dev/disk2 conv=sync
diskutil eject /dev/disk2
```

## Boot device

* `sudo raspi-config`
  * set wifi
  * enable ssh
  * timezone

## Run package install

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y curl nodejs npm libavahi-compat-libdnssd-dev
sudo npm install -g --unsafe-perm homebridge
sudo npm install -g npm@latest
sudo npm update npm -g
```

## Install GPIO control plugin for Homebridge
```
cd /tmp; wget https://lion.drogon.net/wiringpi-2.50-1.deb; sudo dpkg -i wiringpi-2.50-1.deb; cd -
sudo npm install -g --unsafe-perm homebridge-gpio-device
```

## Run Homebridge once and pair
`homebridge`

## Make it run on boot

```bash
sudo npm install -g pm2 --unsafe-perm
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u pi --hp /home/pi
pm2 start homebridge
pm2 save
```

## Configure GPIO pins
```
vi .homebridge/config.json

{
  "bridge": {
    "name": "Name",
    "username": "Mac",
    "port": 51826,
    "pin": "Some-pin"
  },

  "description": "Apartment automation system via RaspberryPi Zero",
  "accessories": [
		{
			"accessory": "GPIODevice",
			"name": "Roller Shade",
			"type": "WindowCovering",
			"inverted": true,
			"pins": [22,21],
			"shiftDuration": 1,
			"initPosition": 99
		}
  ],

  "platforms": [ ]
}
```

## Developer Notes
* https://github.com/KhaosT/HAP-NodeJS - the low level Homekit layer under https://github.com/nfarina/homebridge
