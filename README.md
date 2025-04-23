# Raspberry-Pi-24-7-BeeCamera

## Livestream night vision camera, embedded on website

## Introduction
The Raspberry Pi BeeCamera is a work project requested by the Plains Art Museum. The main goal of this project is to install an OV5647 5MP 1080p Arducam Day-Night IR camera in/a honeybee hive to capture these busy bees at work.

The objective is to have a Raspberry Pi 4 and the OV5647 run 24/7 and livestream the video feed to a website.

My goal is to make this project as replicable as possible, especially after having little to no experience with Raspberry Pi prior to starting this project. 

All you really need here is a basic understanding of Raspberry Pi boards.

## - Hardware
- A Raspberry Pi 4, 3 B+
- OV5647 ArduCam with Built-in Motorized IR-CUT Filter & Two Infrared LEDs for Raspberry Pi https://www.arducam.com/product/arducam-for-raspberry-pi-noir-5mp-ov5647-camera-module-motorized-ir-cut-filter-for-daylight-and-night-vision-support-pi-4-zero-pi-3/
- A 500 GB micro SD card (Obviously not necessary depending on what you do with this project)
- Mouse, Keyboard, and Monitor

## - Software
- Raspberry Pi OS (Legacy) with desktop and recommended software
- 32-bit Kernel version 6.1 Debian version 11 (bullseye)

## - Download
| Model  | OS |
| ------------- | :-----:  |
| Raspberry Pi 4 & 3B+ | [image](https://www.raspberrypi.com/software/operating-systems/)
- This should take you to the downloads page for the OS images

## - Flashing
Once you have your OS image downloaded, you can go ahead and flash it onto the SD card. You should have a good-quality SD card with at least 64 GB for this project. You can do this with the Raspberry Pi Imager https://www.raspberrypi.com/software/

## - Your first boot
You want to insert your new microSD into the slot on your Raspberry Pi and power it. Then connect it to Wi-Fi once it has finished booting, then open up the console/terminal and type the commands below.

```python
sudo apt-get update
sudo apt-get upgrade
```

Next, if you haven't already, I suggest running the command below so you can set up your system configurations how you want.
```python
sudo raspi-config
```
## - Camera

Next, I set up my camera for this project. When accessing the configuration settings, like I mentioned before, you want to go down to the 'Interface Options' and then enable Legacy Camera. 

Note: The Bullseye OS uses Legacy Camera Support, and the Bookworm OS no longer has Legacy Camera Support.

Please look into your camera versions and make sure they work with your OS.

- I have found that any camera that is Version 3 only works with libcamera libraries, so you should use Bookworm.
- https://www.waveshare.com/wiki/RPi_NoIR_Camera_V2
  
This website has a good list of cameras and what their supported driver type is.

Some other websites for camera documentation.
- https://www.raspberrypi.com/documentation/accessories/camera.html#:~:text=The%20original%205%2Dmegapixel%20model,which%20was%20released%20in%202023.
- https://www.raspberrypi.com/documentation/computers/camera_software.html

Once your camera is set up, you should restart your system. This can simply be done by using the command below.
Sudo reboot or Sudo Shutdown -r now, this is only if your system doesn't prompt you to restart after making the change.

Next, you can test out your camera on your console to see if it works. I am using a terminal emulator via SSH, so this does not work unless I am connected directly to a desktop such as a Raspberry Pi 7" Touchscreen, which is what I am using to see if the camera works.

To ensure your camera is connected and active, use the command

vcgencmd get_camera

It should say supported=1 detected=1, if the camera is connected, or else detected = 0. Make sure the ribbon cable is attached properly and that it is connected to the Camera Serial Interface(CSI), not the Display Serial Interface(DSI). The ribbon connector will fit into either port. The camera port is located near the HDMI connector.

There are many ways to see if the camera works, such as using Libcamera-hello, rpicam-hello, raspistill -o Desktop/image.jpg, etc. You will have to figure out which one works for you, as every camera is different; the one that worked for me was raspistill.

## - Dataplicity


## - Basic Security Set Up



## - Work with credential files (Optional)



## - Static IP (Optional)




## - Getting Permissions and Folders set up




## - Systemd Setup

Step 1: Setting up the script to run at boot/

- Systemd is a wonderful resource when it comes to running files at boot, and it is a library that is a part of the Raspberry Pi OS, so you do not need to download anything for this.
- https://www.thedigitalpictureframe.com/ultimate-guide-systemd-autostart-scripts-raspberry-pi/ 
- This is a website that has a nice guide on how to use the system. 
- First, you want to type
    - **sudo nano /etc/systemd/system/name-of-your-service.service**. 
- Then you will want to place this inside your file


        [Unit] 
        Description=Motion Detection 
        After=multi-user.target 
        Wants=name-of-your-service.service
  
        [Service] 
        Type=simple 
        WorkingDirectory=/home/your-user 
        ExecStart=/usr/bin/python3 /home/your-user/python_scripts/your_script_name.py 
        User=your-user 
        KillMode=mixed 

        # Restart Policies 
        Restart=always 
        RestartSec=3 

        [Install] 
        WantedBy=multi-user.target

- Next, you want to save and exit.
- Then you need to change the file permissions, do this by typing in your console
     - sudo chmod 644 /etc/systemd/system/name-of-your-service.service
 - As the last step, you need to tell the system that you have added this file; this will make sure that this service starts at boot.
 - Type 'sudo systemctl daemon-reload' and then 'sudo systemctl enable name-of-your-service.service'
 - You can always disable the service by typing 'sudo systemctl disable name-of-your-service'
 - Otherwise, the website link I provided goes through the list of commands that you should useto run this service and manage it.

Note: I created the folder python_scripts, so do not add /python_scripts/ if you do not plan on putting your python scripts in a specific folder. The above is simply the path to my file, everyone has a different path to their files.


## - Some Errors You May Run Into
There is a chance you will have permission errors or USB issues. The USB issue I am referring to typically occurs when the system is shut down unsafely, the USB is not unmounted properly, or when the system crashes. This doesn't occur every time these happen, however, that does not mean it is not going to happen.

The system usually creates and removes folders automatically if you plug in an external device. For example, Ubuntu crashed, therefor, the system can't remove the folder, and the next created folder becomes a suffix, the number 1.

I was able to determine this by checking the status of the systemd service when it was running, it told me there was a broken pip Errno 32.

However, what I found from this was that typically this occurs when the audio is not set up correctly. I know that my audio was setup correctly, so I knew this was not the issue. I then decided to run the motion.py file in Thonny, here I was able to see that after motion detection started it immediately said permission denied. So, I knew that my permissions were messed up (or so I thought). After looking at the /media/your-user folder I was able to determine that two folders were created under the same name.
This took me some time to figure out, but after seeing the obvious problem I was able to come up with a very simple solution.

Unmount your external flash drive and check the folder in /media/your-user again. Now, there should only be one (empty) folder. Remove the folder with sudo rights:

sudo rmdir /media/your-user/your-usb

## - Creating a Cron Job

Step 1: Open the Crontab (make sure to do 'cd/home/your_username) first
- sudo crontab -e

Step 2: Add the cron job
- 0 3 */2 * * /home/your_username/clean_system.sh > /home/your_username/clean_system.log 2>&1
- 0 3 * * * /home/your_username/update_system.sh > /home/your_username/update_system.log 2>&1

Step 3: Save & Exit (ctrl + x, then y, then exit)

Step 4: Update .bashrc
To access, do (sudo nano ~./bashrc)
Then scroll to the bottom of the file and add the following lines...

      # Display cron job message 
      if [-f /home/your_username/cron_message.txt]; then 
          cat /home/your_username/cron_message.txt 
      fi 
      
      # Display system update and upgrade message 
      if [-f /home/your_username/update_message.txt]; then 
          cat /home/your_username/update_message.txt 
      fi  

Now we need to create the bash scripts

Step 5: Create the clean/update script

- sudo nano clean_system.sh

Then type the following lines

  #!/bin/bash 
  #Clean the system of unnecessary caches 
  sudo apt clean 
  
  #Log the system clean to a file 
  echo "System has been cleaned on $(date)" > /home/your_username/cron_message.txt 


Note: you will need to manually type '-y', otherwise it won't be identified/recognized properly.

Now save and exit

Step 6: Make the scripts executable

- sudo chmod +x update_system.sh
- sudo chmod +x clean_system.sh

Step 7: Verify the setup (check if the cron job is listed)

- sudo crontab -l

Step 8: Test the script manually

- /home/your_username/update_system.sh
- /home/your_username/clean_system.sh

Step 9: Reboot your device so it can update the new changes made

- sudo reboot

Step 10: Verify the cron job is running

- crontab -l

Step 11: Ensure files have privileges

- sudo chown your_user:your_user /home/your_username/update_system.sh
- sudo chown your_user:your_user /home/your_username/clean_system.sh
- sudo chown your_user:your_user /home/your_username/cron_message.txt
- sudo chown your_user:your_user /home/your_username/update_message.txt


# - Log Rotation
This is a must-have when creating new files or processes like systemd and cron jobs. The below were my preferences, so if you wish to do something else, you can still follow this section, but change it in your system as needed.

Step 1: Modify logrotate.conf

- cd /etc
- sudo nano logrotate.conf

Change weekly to daily & rotate 2 (was 4), then save and exit.

Step 3: Create a new file

- sudo nano etc/logrotate.d/journal_log

Add the following lines

- /var/log/journal/some long number/*.journal* { 
    - Note: 'some long number' can be found by typing in the system "exit, re-login, then type 'ncdu /' and go to the /var (use the arrow keys here.)" 
-However, if you aren't in any files, you just need to type 'ncdu' and the steps after.

Then go to /log, then /journal, there you will see the file name you need to copy. Should be in similar length to the one above. Going back to the file.  Type 'ctrl c', then 'cd /home/your_user', then look back at the beginning of 'step 3' to access the file again. Remember to replace the long folder name with your own now, I have shown you how to do it in 'step 3'. 


Add the following lines to the rest of your file 

    rotate 2 
    hourly 
    missingok 
    notifempty 
    nocompress 
    maxage 48 
    post rotate 
            rm –f /var/log/journal/really long file name goes here/*.journal* 2>/dev/null; 
    endscript 
    } 

 

So the entire file should look like the below. 


/var/log/journal/some long number/*.journal* { 

    rotate 2 
    hourly 
    missingok 
    notifempty 
    nocompress 
    maxage 48 
    post rotate 
            rm –f /var/log/journal/really long file name goes here/*.journal* 2>/dev/null; 
    endscript 
    } 


Step 4: Forcing log rotation (!VERY IMPORTANT/USEFUL!)

- sudo logrotate -f /etc/logrotate.d/rotate -journal

Why is this important?

- Prevents Disk Space Overflow
- Keeps Logs Manageable
- Improve System Performance
- Security and Compliance
- Testing Configuration Changes

Step 5: Create a new file/make a log rotation file for multiple libraries/processes


 

 

  
Step 6: Understanding what's happening in the log rotation files we have created

/var/log/motion/*.log*
/var/log/motioneye/*.log*

What it does:
Targets all log files (including rotated one, like .log.1, .log.2.gz, etc.) in the /var /log/motion/ and /var/log/motioneye/ directories

Meaning:
You are applying the same rotation policy to both sets of logs.

rotate 2

What it does:
Keeps 2 archived log files before deleting old ones.

Example:
If today's logrotates, yesterday's and the day before yesterday's logs will be kept, then the oldest will be deleted

hourly

What it does: 
Rotates the logs every hour. 

Why is it useful: 
Useful for logs that grow rapidly (like motion detection logs) so they don’t clog up disk space. Probably overkill unless you have a high-frequency logging system. 

missingok 

What it does: 
If the log files are missing, logrotate will not throw an error—it will just skip them. 

Why: 
Good for cases where logs might not always exist (e.g., if no motion events occurred). 

Notifempty 

What it does: 
Doesn’t rotate empty log files 
 
Why: 
Saves unnecessary effort rotating logs with no data. 

nocompress 

What it does: 
Disables compression of rotated logs. 

Why: 
Maybe you want quick access to old logs without unzipping. Compression saves space but slows down quick access. ]

maxage 48 

What it does: 
Deletes any log files older than 48 hours. 
 
Why: 
Ensures logs don't pile up forever. This works alongside the rotate 2 option for cleanup. 

postrotate 
    rm –f /var/log/motion/*.log* 2>/dev/null; 
    rm –f /var/log/motioneye/*.log* 2>/dev/null; 
endscript 

What it does: After rotating logs, it forcefully deletes all logs matching those patterns. 
- rm -f: Force remove. 
- 2>/dev/null: Suppress errors if files aren’t found. 

Why: 
Seems redundant (since logrotate already handles deletion), but maybe intended to clear out stray or orphaned logs that didn’t get cleaned properly. 


Summary: 

This config: 

- Rotates logs every hour. 
- Keeps 2 old logs. 
- Deletes logs older than 48 hours. 
- Skips missing or empty logs. 
- Doesn’t compress logs. 
- Cleans up all related logs after rotation. 

That is everything you should need to get this working! Make sure to save and exit! 



## - Resources



## -  Final Thoughts
I think this was a very fun project, as difficult as it was getting into it at first, it ended up not being so bad. At least that is now that I know how to do it. For anybody looking for a similar project that is cost-effective (free), this is definitely the project for you.

I also wanted to give a special thanks to my mentor, Joseph Rinehart. Without the resources and support from him and the USDA, I would not have been able to complete this project. 
