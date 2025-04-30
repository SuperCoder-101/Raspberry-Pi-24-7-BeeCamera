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

## - MotionEye Setup
Set up MotionEye.eo
Source: https://jmichault.github.io/motioneye.eo-dok/en/instalado_en_debian/
We need to modify some things here for MotionEye.

**1.	Install MotionEye.eo.**

**2.	Edit the config:**

`sudo nano /etc/motioneye/motioneye.conf`

Set the port:

`port 80`

Make sure to replace `port 8765`.

**3. Reboot the system:**

`sudo reboot`

**4. Enable wormhole via Dataplicity.**

**5. Edit the systemd file:**

`sudo nano /etc/system/systemd/motioneye.service`

The file should look like the below:
```python
[Unit]
Description=motionEye Server
After=network.target local-fs.target remote-fs.target

[Service]
User=root
RuntimeDirectory=motioneye
LogsDirectory=motioneye
StateDirectory=motioneye
ExecStart=/usr/local/bin/meyectl startserver -c /etc/motioneye/motioneye.co>
Restart=on-abort


# Restart Script
Restart=Always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

**6. Save and Exit.**

**7. Reload and restart the service:**

`sudo systemctl daemon-reload`

`sudo systemctl restart motioneye`

`sudo systemctl status motioneye`

**8. Open the wormhole link to access the web portal.**
    

## - Logging for MotionEye

**1. Create log directory:**

```python
sudo mkdir -p /var/log/motioneye
sudo chown root:root /var/log/motioneye
sudo chmod 755 /var/log/motioneye
```

**2. Edit motioneye.conf:**
`sudo nano /etc/motioneye/motioneye.conf`

Add:

```python
log_level info
log_file /var/log/motioneye/motioneye.log
```

**3. Make sure the log file itself exists:**
Even though MotionEye should create it automatically, we’ll be proactive:

```python
sudo touch /var/log/motioneye/motioneye.log
sudo chown root:root /var/log/motioneye/motioneye.log
sudo chmod 644 /var/log/motioneye/motioneye.log
```

Now MotionEye can open and write into the file cleanly without error.

**4. Reload and Restart MotionEye:**

```python
sudo systemctl daemon-reload
sudo systemctl restart motioneye
```

Fresh config, fresh logging, service starts without explosions.

**5. Check logs:**
After a few seconds/minutes, you can check that MotionEye is logging stuff like:

```python
cat /var/log/motioneye/motioneye.log
# or
tail -f /var/log/motioneye/motioneye.log
```

## - Install a firewall 
UFW (Uncomplicated Firewall) is a simple interface for managing iptables on Raspberry Pi OS.

**1. Install UFW**

`sudo apt install ufw` 

By default, UFW blocks all incoming connections and allows all **outgoing**.

**2. Open SSH Port (default is 22)**

`sudo ufw allow 22` 

If you're setting up a **static IP**, you can restrict it to a specific IP.

`sudo ufw allow from 192.168.1.100 port 22`  

(Replace `192.168.1.100` with your actual device IP.)

**3. Enable UFW**

`sudo ufw enable` 

Check rules:

`sudo ufw status verbose`

Optional: View added rules

`sudo ufw show added`

 **Warning:** If you mess this up and lock out SSH access, you'll need a monitor and keyboard to regain control. Be careful.
 
## - Google Authenticator for 2FA

**1. Install the PAM moldule**

`sudo apt install libpam_google_authenticator`

**2. Run setup**

`sudo google-authenticator`

Follow the prompts:
  - Scan the QR coe with the **Google Authenticator** app
  - Answer the questions (`y` recommended for all)

**3. Enable PAM module**

Edit SSH's PAM config:

`sudo nano /etc/pam.d/sshd`

Add to the **top** of the file:

`auth required pam_google_authenticator.so`

Save and exit.

**4. Enable 2FA in SSH**

Edit the SSH server config:

`sudo nano /etc/ssh/sshd_config`

Find or add these lines:

`ChallengeResponseAuthentication yes`

Restart SSH:

`sudo systemctl restart ssh`


## - Change SSH Port Number (Optional)
To change your SSH port:

`sudo nano /etc/ssh/sshd_config`
- Look for `port 22`. Change it to your preferred port (e.g., `Port 2200`)
- If it's not there, add:
    `port 2200` (or any unused port)

Save and exit. Then restart SSH:

`sudo service ssh restart`

**Reminder:**
- Use `sshd_config`, not `ssh_config`
- Make sure the new port is allowed in your firewall


## - Static IP (Optional)

!IMPORTANT! 

IF YOU SET UP A STATIC IP ADDRESS WITH A SPECIFIC WIFI NETWORK THAT STATIC IP ADDRESS CAN ONLY WORK ON THAT WIFI NETWORK. MEANING YOUR DEVICE WILL RUN INTO LOGIN ISSUES VIA SSH IF YOU TRY TO USE IT WITH A DIFFERENT WIFI NETWORK, THIS IS BECAUSE THE STATIC IP ADDRESS WAS NOT SET UP ON THAT SPECIFIC NETWORK. 

 PLEASE ONLY DO THIS IF YOU WANT TO, DO NOT DO THIS UNLESS YOU FEEL IT IS NECESSARY. 


Setting up a static IP address 

- https://phoenixnap.com/kb/raspberry-pi-static-ip 


I will also just write down what I take from this guide.
 

Obtain Current IP Address 

`Hostname –I` 


OR 

On your mobile hotspot or Raspberry Pi display you can find the IP address easily. 

The Raspberry Pi display if you enable RealVNC Server the IP address of your Raspberry Pi will show up there. 


Next you want to identify the default network interface 

`ip r | grep default` 


The output will display the router's address. To obtain the name of your network interface, use the following command below: 

`route | grep '^default' | grep –o '[^]*$'` 


This command uses grep regex to extract the interface name from the larger output. 


Next you want to obtain the DNS Address, you can find it in the resolv.conf file located in the /etc directory. 

`sudo nano /etc/resolv.conf` 


Look for the line that starts with the word nameserver and write down the DNS IP address. 

Also the nameserver in this case is literally the same as the IP address. 


Next you want to edit the network settings 

Once you have all this information, set up a static private IP address on your Raspberry Pi employing one of the two methods described below. 


Open the dhcpcd.conf file in a text editor. 

`sudo nano /etc/dhcpcd.conf` 


Scroll to the bottom of the file and find the lines below.  


Here you will want to uncomment the lines you plan on using and make sure to add your ip addresses and other configurations as needed. 

 So it will kinda look like the picture below. 


After this has been done you will save and exit.  


HOWEVER DO NOT SHUTDOWN OR REBOOT THE SYSTEM YET. 


Remember when we made that firewall? Yea you need to add your new static ip address to that or you will be locked out of your device via ssh. 


Simply go back to the firewall tutorial to the left and add your new IP address following the steps listed. 


Afterwards you will  reboot the Raspberry Pi 

`sudo reboot` 


Then you can just test your Raspberry Pi like so 


`hostname -I` 


Then everything should be ready to go with your static IP address. I also wouldn't follow the last bit of the tutorial from the link I sent where it says to set up the static IP address via GUI. The Raspberry Pi has had quite a few updates to their GUI since this tutorial came out and they do not have their settings set up like that anymore on the display.  


Otherwise you're done here. 


## - Folder & Script Permissions

**1. Create a Folder**

```python
sudo mkdir /home/your-user/python_scripts
sudo chown -R your-user:your-user /home/your-user/python_scripts
```

**2. Open Script in Thonny**

- Go to Applications > Programming > Thonny
- Load your script from the flash drive
- Modify or test as needed

3. Set Permissions

```python
sudo chmod 775 /home/your-user
cd /home/your-user/python_scripts
sudo chmod 775 your_file.py
```

Back out:

`cd`

**4. Fix Device Permissions**

If the camera isn't working, run:

`sudo chmod 666 /dev/video0`

And for flash drive access:

`sudo chmod 775 /media/your-user/flash_drive`


## - Systemd Setup

Step 1: Setting up the script to run at boot/

- Systemd is a wonderful resource when it comes to running files at boot, and it is a library that is a part of the Raspberry Pi OS, so you do not need to download anything for this.
- https://www.thedigitalpictureframe.com/ultimate-guide-systemd-autostart-scripts-raspberry-pi/ 
- This is a website that has a nice guide on how to use the system. 
- First, you want to type
    - `sudo nano /etc/systemd/system/name-of-your-service.service`. 
- Then you will want to place this inside your file

```python
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
```

- Next, you want to save and exit.
- Then you need to change the file permissions, do this by typing in your console
     - sudo chmod 644 /etc/systemd/system/name-of-your-service.service
 - As the last step, you need to tell the system that you have added this file; this will make sure that this service starts at boot.
 - Type 'sudo systemctl daemon-reload' and then 'sudo systemctl enable name-of-your-service.service'
 - You can always disable the service by typing 'sudo systemctl disable name-of-your-service'
 - Otherwise, the website link I provided goes through the list of commands that you should useto run this service and manage it.

Note: I created the folder python_scripts, so do not add /python_scripts/ if you do not plan on putting your python scripts in a specific folder. The above is simply the path to my file, everyone has a different path to their files.


## - Troubleshooting USB or Permission Errors

If you crash or unplug incorrectly, USB mounts can get messy.

Symptoms:
- MotionEye erros
- `Errno 32`
- "Permission denied" in scripts

Fix:
1. Unplug USB drive
2. Check /media/your-user/
3. If a duplicate folder exists, remove it:

`sudo rmdir /media/your-user/your-usb`


## - Creating a Cron Job

**1. Open the Crontab (make sure to do 'cd/home/your_username) first**
- `sudo crontab -e`

**2. Add the cron job**
- `0 3 */2 * * /home/your_username/clean_system.sh > /home/your_username/clean_system.log 2>&1`
- `0 3 * * * /home/your_username/update_system.sh > /home/your_username/update_system.log 2>&1`

Step 3: Save & Exit (ctrl + x, then y, then exit)

**4. Update .bashrc**
To access, do (sudo nano ~./bashrc)
Then scroll to the bottom of the file and add the following lines...

```python
      # Display cron job message 
      if [-f /home/your_username/cron_message.txt]; then 
          cat /home/your_username/cron_message.txt 
      fi 
      
      # Display system update and upgrade message 
      if [-f /home/your_username/update_message.txt]; then 
          cat /home/your_username/update_message.txt 
      fi  
```

Now we need to create the bash scripts

**5. Create the clean/update script**

- `sudo nano clean_system.sh`

Then type the following lines

```python
  #!/bin/bash 
  #Clean the system of unnecessary caches 
  `sudo apt clean` 
  
  #Log the system clean to a file 
  `echo "System has been cleaned on $(date)" > /home/your_username/cron_message.txt` 
```

Note: you will need to manually type '-y', otherwise it won't be identified/recognized properly.

Now save and exit

**6. Make the scripts executable**

- `sudo chmod +x update_system.sh`
- `sudo chmod +x clean_system.sh`

**7. Verify the setup (check if the cron job is listed)**

- `sudo crontab -l`

**8. Test the script manually**

- `/home/your_username/update_system.sh`
- `/home/your_username/clean_system.sh`

**9. Reboot your device so it can update to the new changes made**

- `sudo reboot`

**10. Verify the cron job is running**

- `crontab -l`

**11. Ensure files have privileges**

- `sudo chown your_user:your_user /home/your_username/update_system.sh`
- `sudo chown your_user:your_user /home/your_username/clean_system.sh`
- `sudo chown your_user:your_user /home/your_username/cron_message.txt`
- `sudo chown your_user:your_user /home/your_username/update_message.txt`


# - Log Rotation
This is a must-have when creating new files or processes like systemd and cron jobs. The below were my preferences, so if you wish to do something else, you can still follow this section, but change it in your system as needed.

Step 1: Modify logrotate.conf

- `cd /etc`
- `sudo nano logrotate.conf`

Change weekly to daily & rotate 2 (was 4), then save and exit.

Step 3: Create a new file

- sudo nano etc/logrotate.d/journal_log

Add the following lines

- `/var/log/journal/some long number/*.journal* {`
    - Note: 'some long number' can be found by typing in the system "exit, re-login, then type 'ncdu /' and go to the /var (use the arrow keys here.)" 
-However, if you aren't in any files, you just need to type 'ncdu' and the steps after.

Then go to /log, then /journal, there you will see the file name you need to copy. Should be in similar length to the one above. Going back to the file.  Type 'ctrl c', then 'cd /home/your_user', then look back at the beginning of 'step 3' to access the file again. Remember to replace the long folder name with your own now, I have shown you how to do it in 'step 3'. 


Add the following lines to the rest of your file 

```python
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
```
 

So the entire file should look like the below. 

```python
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
```

Step 4: Forcing log rotation (!VERY IMPORTANT/USEFUL!)

- sudo logrotate -f /etc/logrotate.d/rotate -journal

Why is this important?

- Prevents Disk Space Overflow
- Keeps Logs Manageable
- Improve System Performance
- Security and Compliance
- Testing Configuration Changes

Step 5: Create a new file/make a log rotation file for multiple libraries/processes

`sudo nano /etc/logrotate.d/motion_log`

add the following lines to this file

```python
/var/log/motion/*.log*
/var/log/motioneye/*.log*{

    rotate 2
    hourly
    missingok
    notifempty
    nocompress
    maxage 48
    postrotate
           rm -f /var/log/motion/*.log* 2>/dev/null;
           rm -f /var/log/motioneye/*.log* 2>/dev/null;
    endscript
}
```

Make sure to save and exit!
 
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
If today's log rotates, yesterday's and the day before yesterday's logs will be kept, then the oldest will be deleted

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
