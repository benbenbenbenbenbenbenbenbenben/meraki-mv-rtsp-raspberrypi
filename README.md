# Meraki MV RTSP > Raspberry Pi

Stream Meraki MV Camera video on a Raspberry Pi. This uses the opensource IP monitoring solutuon: rpisurv https://github.com/SvenVD/rpisurv

## Use Case
You require low-latency streams that RTSP provides or require several monitors to be always on with multiple streams. This simple deployment can enable you to have a Raspberry Pi in the back of each TV.

<p align="center">
  <img width="460" height="300" src="https://github.com/benbenbenbenbenbenbenbenbenben/meraki-mv-rtsp-raspberrypi/blob/master/preview.jpg?raw=true">
</p>

## Table of Contents
- [What You Need](#what-you-need)
- [Meraki Dashboard](#meraki-dashboard)
- [Raspberry Pi](#raspberry-pi)
- [Rpisurv](#rpisurv)
- [Final](#final)
- [Troubleshooting](#troubleshooting)

## What You Need
1. Raspberry Pi (3 or 4)
2. Monitor with HDMI
3. SD Card Reader
4. MV Camera/s

## Meraki Dashboard
1. Ensure MV Firmware is 4.2+
2. Enable RTSP per camera: Cameras -> Camera -> Settings -> Video Settings -> External RTSP -> yes
3. Make a note of your RTSP stream URL: rtsp://192.168.0.127:9000/live

<p align="center">
  <img height="80" src="https://github.com/benbenbenbenbenbenbenbenbenben/meraki-mv-rtsp-raspberrypi/blob/master/rtsp.png?raw=true">
</p>

Read more about Meraki MV RTSP:
https://documentation.meraki.com/MV/Advanced_Configuration/External_RTSP

## Raspberry Pi
1. Connect SD Card to Mac/PC
2. Download the Raspberry Pi Imager
        https://www.raspberrypi.org/downloads/
3. Run above imager, select Rasbian as OS, select your SD Card, write
<p align="center">
  <img height="80" src="https://github.com/benbenbenbenbenbenbenbenbenben/meraki-mv-rtsp-raspberrypi/blob/master/imager.png?raw=true">
</p>

4. Once finished go to the SD Card and create a file in the root directory called **ssh** (on Mac **touch ssh**)
        *This will enable ssh on the raspberry pi so we can remotely connect without a keyboard*
5. Put the SSD card in the Raspberry Pi
6. Connect Raspberry Pi: ethernet, hdmi and power.
7. Once powered on get the IP address of the pi (from router or use arp -a on Mac)
8. Ssh to raspberry pi, username is pi and password is raspberry
```bash
    $ ssh pi@x.x.x.x
```

## Rpisurv
Opensource IP surveillance for Raspberry Pi.

1. If you are going to have multiple streams, add gpu_mem=512 to your /boot/config.txt.
```bash
    $ sudo nano /boot/config.txt
```
*nano tips: save = ctrl + o, exit = ctrl + x*

2. git clone rpisurv repository
```bash
    $ git clone https://github.com/SvenVD/rpisurv
```
3. Move into folder 
```bash
    $ cd rpisurv
```
4. Run the install script
```bash
    $ sudo ./install.sh
```
5. Edit **surveillance.yml** file to display the RTSP feeds [Example File](https://github.com/benbenbenbenbenbenbenbenbenben/meraki-mv-rtsp-raspberrypi/blob/master/surveillance.yml)
```bash
    $ nano /usr/local/bin/rpisurv/conf/surveillance.yml 
```
*nano tips: save = ctrl + o, exit = ctrl + x*

6. Lastly Reboot the Raspberry Pi
```bas
    $ sudo reboot
```

## Final
Once the Raspberry Pi reboots you should see your video streams. 
If you make any changes to surveillance.yml file you must restart the service.
```bash
    $ sudo systemctl restart rpisurv
```

## Troubleshooting
Start by checking the logs:
```bash
    $ journalctl -u rpisurv | tail -n 30
```
If none of the common errors below help, check rpisurv github and community pages.
https://github.com/SvenVD/rpisurv

### Common Errors
1. Service does not start, stuck at console screen.
Check log for:
```
found character '\t' that cannot start any token
```
YAML file must use spaces not tabs. Copy the surveillance.yml file to a YAML validator like http://www.yamllint.com/ to find the errors.

2. Stream starts and stops
Check log for:
```
Free gpu mem is 0.0 bytes which is less than 80000000 bytes
```
The Raspberry Pi is running out of memory, make sure to increase the gpu memory mentioned above.
If same issue try with 1 RTSP, if this works fine you will need to decrease the quality settings of the camera.
Try changing from 1080P to 720P from the Meraki Dashboard.
Cameras -> Camera -> Settings -> Quality and Retention
<p align="center">
  <img height="80" src="https://github.com/benbenbenbenbenbenbenbenbenben/meraki-mv-rtsp-raspberrypi/blob/master/quality.png?raw=true">
</p>
