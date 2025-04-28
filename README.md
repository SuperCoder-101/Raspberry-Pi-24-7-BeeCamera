# Raspberry-Pi-24-7-BeeCamera

## Livestream night vision camera, embedded on website

## Introduction
The Raspberry Pi BeeCamera is a work project requested by the Plains Art Museum. The main goal of this project is to install an OV5647 5MP 1080p Arducam Day-Night IR camera in/a honeybee hive to capture these busy bees at work.

The objective is to have a Raspberry Pi 4 and the OV5647 run 24/7 and livestream the video feed to a website.

My goal is to make this project as replicable as possible, especially after having little to no experience with Raspberry Pi prior to starting this project. 

All you really need here is a basic understanding of Raspberry Pi boards.

## - Hardware
- A Raspberry Pi 4 (or 3 B+)
- OV5647 ArduCam with Built-in Motorized IR-CUT Filter & Two Infrared LEDs for Raspberry Pi https://www.arducam.com/product/arducam-for-raspberry-pi-noir-5mp-ov5647-camera-module-motorized-ir-cut-filter-for-daylight-and-night-vision-support-pi-4-zero-pi-3/
- A 500 GB micro SD card (Optional Size)
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

## - Your First Boot
You want to insert your new microSD into the slot on your Raspberry Pi and power it. Then connect it to Wi-Fi once it has finished booting, and then open up the console/terminal and type the commands below.

```python
sudo apt-get update
sudo apt-get upgrade
```

Next, if you haven't already, I suggest running the command below so you can set up your system configurations how you want.
```python
sudo raspi-config
```
## - Camera
- Enable Legacy Camera Support (only needed for older cameras like OV5647)
- Check Raspberry Pi Camera Documentation
- Verify Connection:
```python
vcgencmd get_camera
```
- Test camera capture:
```python
raspistill -o Desktop/image.jpg
```
- Note: The command above may not work; there are many commands online that you can find to test your camera.
- Important: Bookworm OS uses libcamera; Bullseye supports legacy Camera.

## - Dataplicity
To set up Dataplicity on the Raspberry Pi, you will need to enter your email on the website first, then a download link will appear for the Pi.
- Click on this link to get to the website
  - https://www.dataplicity.com/
- Then simply log in and follow the steps to get your Raspberry Pi set up with Dataplicity
  
Note: Using Dataplicity is not very difficult; there are plenty of documents and articles on the website that you can use to help you get moving.

## - MotionEye.eo





## - Basic Security Set Up

This is for changing the default user 

Change default username : 

sudo useradd –m JUSTME –G sudo 

JUSTME being the user name, you can pick whatever you want. 

Next, enter: 

sudo passwd JUSTME 

This will allow you to set a password for the new user. Your new account should now have the same permissions as pi, as both are in the sudo usergroup. 

Before deleting the user pi, logout and then log in again using your new account, and attempt to run: 

sudo visudo 

If successful, you can delete the default pi user. In the terminal, enter 

sudo deluser pi 

If you want, you can also simultaneously remove the /home/pi directory 

sudo deluser –remove-home pi 


## - Install a firewall 

There are a number of ways to add a firewall to your Raspberry Pi, including the iptables that comes with Raspberry Pi OS. I would recommend to use the UFW ('uncomplicated firewall') interface. 

To install the UFW software, open a terminal window and enter: 

sudo apt install ufw 

UFW will be installed but not active yet. Also by default it will block all incoming traffic and allow all outgoing traffic, this includes any SSH connections. 

To open a port whil using UFW, such as port 22, the default used for SSH, type in: 

sudo ufw allow 22 

You can also make it more specific to only allow specific IP-addresses: 

sudo ufw allow from 192.168.1.100 port 22  

Please keep in mind that this is just a made up port number please only do this for your IP-address specifically if you plan to create a STATIC IP address.  

Don't forget to replace values with your own settings. On a local network you can get your ip address with the command ipconfig (Windows) or ifconfig(Linux/Mac). 

To list the enabled firewall rules: 

Sudo ufw show added 

Now to enable the firewall: 

Sudo ufw enable 

Be careful as this will enable the firewall now, and you will get the message Firewall is active and enabled on system startup. 

To display your current rules once ufw enabled, use this command: 

sudo ufw status verbose 

Quite complicated rules can be provided, such as to allow specific IP addresses to be blocked, specifying in which direction traffic is allowed, or limiting the number of attempts to connect. For more complex configurations, I suggest to check the manual, just type: 

man ufw 

## - Work with credential files (Optional)

The final security recommendation (for now) is to make use of environmental variables to store credentials, such as email logins, that may be needed in user scripts. Environment variables are operating system level variables whose value can be used by software programs. As the values remain the system, not in the script, there is less risk of exposing credentials. 

 
Let’s create a simple file called mycredentials: 

nano ~/.mycredentials.env 


Now enter any information you may want and use a variable name you can call upon in your scripts prepended with an export command. For example: 

export GMAIL_USERNAME='XXXXXXXX' 

export GMAIL_PASSWORD='XXXXXXXX' 


Now save the file and change its permissions so it is not readable by others: 

chmod 600 ~/.mycredentials.env 
 

Make sure the variables are loaded: 

source ~/.mycredentials.env 
 

And finally, adapt your script to use the stored variables. For example, in Python: 

Import os 

GMAIL_USERNAME = os.environ['GMAIL_USERNAME'] 

GMAIL_PASSWORD =  os.environ['GMAIL_PASSWORD'] 
 

That is all for this section. 


## - Static IP (Optional)

!IMPORTANT! 

IF YOU SET UP A STATIC IP ADDRESS WITH A SPECIFIC WIFI NETWORK THAT STATIC IP ADDRESS CAN ONLY WORK ON THAT WIFI NETWORK. MEANING YOUR DEVICE WILL RUN INTO LOGIN ISSUES VIA SSH IF YOU TRY TO USE IT WITH A DIFFERENT WIFI NETWORK, THIS IS BECAUSE THE STATIC IP ADDRESS WAS NOT SET UP ON THAT SPECIFIC NETWORK. 

 PLEASE ONLY DO THIS IF YOU WANT TO, DO NOT DO THIS UNLESS YOU FEEL IT IS NECESSARY. 


Setting up a static IP address 

https://phoenixnap.com/kb/raspberry-pi-static-ip 


I will also just write down what I take from this guide.
 

Obtain Current IP Address 

Hostname –I 


OR 

On your mobile hotspot or Raspberry Pi display you can find the IP address easily. 

The Raspberry Pi display if you enable RealVNC Server the IP address of your Raspberry Pi will show up there. 


Next you want to identify the default network interface 

ip r | grep default 


The output will display the router's address. To obtain the name of your network interface, use the following command below: 

route | grep '^default' | grep –o '[^]*$' 


This command uses grep regex to extract the interface name from the larger output. 


Next you want to obtain the DNS Address, you can find it in the resolv.conf file located in the /etc directory. 

sudo nano /etc/resolv.conf 


Look for the line that starts with the word nameserver and write down the DNS IP address. 

Also the nameserver in this case is literally the same as the IP address. 


Next you want to edit the network settings 

Once you have all this information, set up a static private IP address on your Raspberry Pi employing one of the two methods described below. 


Open the dhcpcd.conf file in a text editor. 

Sudo nano /etc/dhcpcd.conf 


Scroll to the bottom of the file and find the lines below.  


Here you will want to uncomment the lines you plan on using and make sure to add your ip addresses and other configurations as needed. 

 So it will kinda look like the picture below. 


After this has been done you will save and exit.  


HOWEVER DO NOT SHUTDOWN OR REBOOT THE SYSTEM YET. 


Remember when we made that firewall? Yea you need to add your new static ip address to that or you will be locked out of your device via ssh. 


Simply go back to the firewall tutorial to the left and add your new IP address following the steps listed. 


Afterwards you will  reboot the Raspberry Pi 

sudo reboot 


Then you can just test your Raspberry Pi like so 


hostname -I 


Then everything should be ready to go with your static IP address. I also wouldn't follow the last bit of the tutorial from the link I sent where it says to set up the static IP address via GUI. The Raspberry Pi has had quite a few updates to their GUI since this tutorial came out and they do not have their settings set up like that anymore on the display.  


Otherwise you're done here. 


## - Getting Permissions and Folders set up

Getting permissions and folders set up 


The WinSCP SOP explains the permission commands in case you want to change the numbers from 777 to 755 for example.   

Step 1: Create a new folder under your user (Cat) or (Your User Name) 

sudo mkdir python_scripts 

Ex: python_scripts (always use _ when creating files, it is easier than dealing with issues related to files with spaces in them). 

Make sure to give permission here as well. 

sudo chown -R Cat:Cat /home/Cat/python_scripts 


Step 2: Open the ‘Applications menu’, hover over ‘Programming’, and click ‘Thonny’. 

This is where you are going to open the Python script.  

Click on Load then go to the flash drive you connect to your Raspberry Pi and select your_file.py. 

Here you can start modifying your script and even run it through Thonny. 

Step 3: Access your terminal/console. 

Now there are a few things we will need to do. 

First type sudo chmod 777 /home/Cat (chmod 777 gives you modify, execute, and write permissions, normally don’t do this however, you will need these permissions to manage and execute this file in the future). 

Next, type cd your_folder, and type sudo chmod 777 your_file.py. This will give that file the permissions it needs to run on your system. 

Now type cd so that you will end up back into the Orchid user directory. 

Note: The python file could be any name, just select the one you plan on using, I am currently using yolo_captureV3.py. 


Step 4: Other file permissions that you might need to add if the camera isn’t working or is being accessed as it should be. 

Type sudo chmod 666 /dev/video0, sometimes this helps with giving the proper permissions to the camera so it can execute. Chmod 666 gives all users, files, etc., read and write permissions. This only gives those permissions to what you write the permissions for so the /dev/video0 directory. 

 Another directory that needs permission is /media/Cat/flash_drive, so type sudo chmod 777 /media/Robo/. 

This allows Raspberry Pi permission to send the video to your flash drive.  

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

## - Resources

- https://jmichault.github.io/motioneye.eo-dok/en/
- https://www.youtube.com/watch?v=-hZ5dwDBMag
- https://raspberrypi.stackexchange.com/questions/109712/how-do-you-change-the-ssh-port-number
- https://www.thedigitalpictureframe.com/ultimate-guide-systemd-autostart-scripts-raspberry-pi/
- https://phoenixnap.com/kb/raspberry-pi-static-ip
- https://medium.com/swlh/setting-up-ssh-and-2fa-on-a-raspberry-pi-4cd7b2f6f4ef
- https://www.dataplicity.com/
- https://www.raspberrypi.com/documentation/computers/camera_software.html#building-rpicam-apps
- https://objsal.medium.com/install-uncomplicated-firewall-to-a-raspberry-pi-76f5c4591651
- https://www.techcoil.com/blog/how-i-built-my-home-raspberry-pi-3-cctv-using-a-motion-eye-os-image-from-home-surveillance/
- https://www.youtube.com/watch?v=l2J8Gfq0tqk
- https://www.reddit.com/r/RASPBERRY_PI_PROJECTS/comments/tq22wg/comment/i2inmzh/
- https://github.com/motioneye-project/motioneye/tree/dev?tab=readme-ov-file#installation

## -  Final Thoughts
I think this was a very fun project, as difficult as it was getting into it at first, it ended up not being so bad. At least that is now that I know how to do it. For anybody looking for a similar project that is cost-effective (free), this is definitely the project for you.

I also wanted to give a special thanks to my mentor, Joseph Rinehart. Without the resources and support from him and the USDA, I would not have been able to complete this project. 
