# Raspberry-Pi-24-7-BeeCamera

When working with a Raspberry Pi the first thing you need to do is ensure the Pi is up to date you can use the commands below.

Sudo apt-get update
Sudo apt-get upgrade

Next if you haven't already I suggest doing 'Sudo raspi-config' so you can setup your system configurations how you want.

Next I setup my camera for this project, the camera I am using is an OV5647 5MP 1080P Day-Night IR ArduCam. I am also using the Raspberry Pi Legacy 32-bit Bullseye bios for this system. 
So when accessing the configuration settings like I mentioned before, you want to go down to the 'Interface Options' then enable Legacy Camera. The reason why I mentioned I am using Bullseye for my bios is that Raspberry Pi is now using Bookworm and Bookworm no longer has Legacy Camera Support.

Once your camera is setup you should restart your system this can simply be done by using the command below.
Sudo reboot or Sudo Shutdown -r now, this is only if your system doesn't prompt you to restart after making the change.

Next you can test out your camera on your console to see if it works, I am using a terminal emulator via SSH so this does not work unless I am connected directly to a desktop such as a Raspberry Pi 7" Touchscreen, which is what I am using to see if the camera works.

To ensure your camera is connected and active use the command

vcgencmd get_camera

It should say supported=1 detected=1, if the camera is connected, or else is detected = 0. Make sure the ribbon cable is attached properly and that it is connected to the Camera Serial Interface(CSI), not the Display Serial Interface(DSI). The ribbon connector will fit into either port. The camera port is located near the HDMI connector.

There are many ways to see if the camera works such as using, Libcamera-hello, rpicam-hello, raspistill -o Desktop/image.jpg, etc. You will have to figure out which one works for you as every camera is different, the one that worked for me was raspistill.

