# Raspberry-Pi-24-7-BeeCamera

## Livestream night vision camera, embedded on website

## Introduction
The Raspberry Pi BeeCamera is a work project requested by the Plains Art Mueseum, the main goal of this project is to install an OV5647 5MP 1080p Arducam Day-Night IR camera in/by a honeybee hive to capture these busy bees at work.

The objective is to have a Raspberry Pi 4 and the OV5647 run 24/7 and livestream the video feed to a website.

My goal is to make this project as replicable as possible, especially after having little to no experience in Raspberry Pi prior to starting this project. 

All you really need here is a basic understanding of Raspberry Pi boards.

## - Hardware
- A Raspberry Pi 4, 3B+
- OV5647 ArduCam with Built-in Motorized IR-CUT Filter & Two Infrared LEDs for Raspberry Pi https://www.arducam.com/product/arducam-for-raspberry-pi-noir-5mp-ov5647-camera-module-motorized-ir-cut-filter-for-daylight-and-night-vision-support-pi-4-zero-pi-3/
- A 500gb micro SD card (Obviously not necessary depending on what you do with this project)
- Mouse, Keyboard, and Monitor

## - Software
- Raspberry Pi OS (Legacy) with desktop and recommended software
- 32-bit Kernel version 6.1 Debain version 11 (bullseye)

## - Download
| Model  | OS |
| ------------- | :-----:  |
| Raspberry Pi 4 & 3B+ | [image](https://www.raspberrypi.com/software/operating-systems/)
- This should take you to the downloads page for the OS images

## - Flashing
Once you have your OS image downloaded, flash it onto the SD card. You should have a good quality SD card with at least 64 GB for this project. You can do this with the Raspberry Pi Imager https://www.raspberrypi.com/software/

## - Your first boot
You want to insert your new Mirco SD into the slot on your Raspberry Pi and power it. Then connect it to Wi-Fi once it has finished booting, then open up the console/terminal and type the commands below.

```python
sudo apt-get update
sudo apt-get upgrade
```

Next if you haven't already I suggest running the commad below so you can setup your system configurations how you want.
```python
sudo raspi-config
```
## - Camera

Next I setup my camera for this project, when accessing the configuration settings like I mentioned before, you want to go down to the 'Interface Options' then enable Legacy Camera. 

Note: The Bullseye OS uses Legacy Camera Support and the Bookworm OS no longer has Legacy Camera Support.

Please look into your camera versions and make sure they work with your OS.

- I have found any camera that is of Version 3 only works with libcamera libraries, so you should use Bookworm.
- https://www.waveshare.com/wiki/RPi_NoIR_Camera_V2
  
This website has a good list of cameras and what their supported driver type is.

Some other websites for camera documentation.
- https://www.raspberrypi.com/documentation/accessories/camera.html#:~:text=The%20original%205%2Dmegapixel%20model,which%20was%20released%20in%202023.
- https://www.raspberrypi.com/documentation/computers/camera_software.html

Once your camera is setup you should restart your system this can simply be done by using the command below.
Sudo reboot or Sudo Shutdown -r now, this is only if your system doesn't prompt you to restart after making the change.

Next you can test out your camera on your console to see if it works, I am using a terminal emulator via SSH so this does not work unless I am connected directly to a desktop such as a Raspberry Pi 7" Touchscreen, which is what I am using to see if the camera works.

To ensure your camera is connected and active use the command

vcgencmd get_camera

It should say supported=1 detected=1, if the camera is connected, or else is detected = 0. Make sure the ribbon cable is attached properly and that it is connected to the Camera Serial Interface(CSI), not the Display Serial Interface(DSI). The ribbon connector will fit into either port. The camera port is located near the HDMI connector.

There are many ways to see if the camera works such as using, Libcamera-hello, rpicam-hello, raspistill -o Desktop/image.jpg, etc. You will have to figure out which one works for you as every camera is different, the one that worked for me was raspistill.

