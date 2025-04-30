# Raspberry-Pi-24-7-BeeCamera

## Live stream night vision camera, embedded on website

## Introduction
The Raspberry Pi BeeCamera is a work project requested by the Plains Art Museum. The main goal of this project is to install an OV5647 5MP 1080p Arducam Day-Night IR camera in/a honeybee hive to capture these busy bees at work.

The objective is to have a Raspberry Pi 4 and the OV5647 run 24/7 and live stream the video feed to a website.

My goal is to make this project as replicable as possible, especially after having little to no experience with Raspberry Pi before starting this project. 

All you need here is a basic understanding of Raspberry Pi boards.

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

Next, if you haven't already, I suggest running the command below so you can set up your system configurations as you want.
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

**1. Install the PAM module**

`sudo apt install libpam_google_authenticator`

**2. Run setup**

`sudo google-authenticator`

Follow the prompts:
  - Scan the QR code with the **Google Authenticator** app
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

**IMPORTANT:**

A static IP will only work on the specific Wi-Fi network you configure it for. If you move the Pi to another network, you'll run into SSH connection issues.

Follow this guide or use the steps below:

- https://phoenixnap.com/kb/raspberry-pi-static-ip 

**1. Get Your Current IP**

`hostname -I`

Or look it up via your router, hotspot, or RealVNC if you're using a display.

**2. Get Your Network Interface**

`ip r | grep default`

Then:

`route | grep '^default' | grep –o '[^]*$'` 

**3. Get Your DNS Server**

`sudo nano /etc/resolv.conf` 

Find the line starting with `nameserver`, note that IP.

**4. Edit dhcpcd.conf**

`sudo nano /etc/dhcpcd.conf` 

Scroll to the bottom. Uncomment and edit the example static IP section:

```python
interface wlan0
static ip_address=192.168.1.150/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1
```

Replace values with yours.

**DO NOT reboot yet!**

**5. Update Firewall Rule for New IP**

If you're using UFW and restricting IPs, allow your new static IP:

`sudo ufw allow from 192.168.1.150 to any port 22`

Then reboot:

`sudo reboot` 

Check it worked:

`hostname -I` 


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

Create a service file:

`sudo nano /etc/systemd/system/your-service.service`

Paste this (edit paths/user as needed):

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

Set permissions:

`sudo chmod 644 /etc/systemd/system/your-service.service`

Enable the service:

```python
sudo systemctl daemon-reload
sudo systemctl enable your-service.service
sudo systemctl start your-service.service
sudo systemctl status your-service.service
```

You can stop or disable it later with:

```python
sudo systemctl stop your-service.service
sudo systemctl disable your-service.service
```

## - Troubleshooting USB or Permission Errors

If you crash or unplug incorrectly, USB mounts can get messy.

Symptoms:
- MotionEye errors
- `Errno 32`
- "Permission denied" in scripts

Fix:
1. Unplug USB drive
2. Check /media/your-user/
3. If a duplicate folder exists, remove it:

`sudo rmdir /media/your-user/your-usb`


## - Creating a Cron Job

**1. Open Crontab**

- `sudo crontab -e`

**2. Add Jobs**

- `0 3 */2 * * /home/your_username/clean_system.sh > /home/your_username/clean_system.log 2>&1`
- `0 3 * * * /home/your_username/update_system.sh > /home/your_username/update_system.log 2>&1`

Save & Exit.

**3. Update `.bashrc` to Display Messages**

`sudo nano ~/.bashrc`

Add at the bottom:

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

**4. Create Bash Scripts**

`sudo nano clean_system.sh`

```python
  #!/bin/bash 
  #Clean the system of unnecessary caches 
  `sudo apt clean` 
  
  #Log the system clean to a file 
  `echo "System has been cleaned on $(date)" > /home/your_username/cron_message.txt` 
```

Save & Exit.

`sudo nano update_system.sh`

```python
#!/bin/bash 
#Update package list and upgrade all packages  

sudo apt update && sudo apt upgrade –y

# Log the update to a file 
echo "System updated at $(date)" > /home/your_username/update_message.txt 
 ```

Save & Exit.

**6. Make the scripts executable**

`sudo chmod +x update_system.sh`
`sudo chmod +x clean_system.sh`

**7. Verify Setup**

`sudo crontab -l`

**8. Test Script**

`/home/your_username/update_system.sh`
`/home/your_username/clean_system.sh`

**9. Reboot Device**

`sudo reboot`

**10. Verify Cronjob is running**

`crontab -l`

**11. Ensure files have privileges**

```python
sudo chown your_user:your_user /home/your_username/update_system.sh
sudo chown your_user:your_user /home/your_username/clean_system.sh
sudo chown your_user:your_user /home/your_username/cron_message.txt
sudo chown your_user:your_user /home/your_username/update_message.txt
```

# - Log Rotation

Log rotation is essential to avoid log overflow and system crashes. We’ll configure two key things:

- System journal logs
- MotionEye logs

**1. Edit Global Settings**

```python
cd /etc
sudo nano logrotate.conf
```

- Change `weekly` to `daily`
- Change `rotate 4` to `rotate 2`

Save & Exit.

**IMPORTANT:** How to find `some-long-number`:

Exit and re-login to your Pi, then run:

`ncdu /`

Use the arrow keys to navigate to `/var/log/journal/` — there you’ll see the long folder name you need.
This step matters because we’re creating a log rotation file for the actual system journal logs, and they live inside that specific directory name.

**2. Create Rotation Configs**

- sudo nano etc/logrotate.d/journal_log

```python
/var/log/journal/some long number/*.journal* { 

    rotate 2 
    hourly 
    missingok 
    notifempty 
    nocompress 
    maxage 48 
    post rotate 
            rm –f /var/log/journal/long file name goes here/*.journal* 2>/dev/null; 
    endscript 
    } 
```

Save & Exit.

`sudo nano /etc/logrotate.d/motion_log`

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

Save & Exit.

**4. Force Log Rotation**

- sudo logrotate -f /etc/logrotate.d/rotate -journal

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
This was a really fun project. It was tough to get started, especially with no prior Raspberry Pi experience, but once I figured things out, it wasn’t so bad. If you're looking for a low-cost (or free) DIY tech project, this one is worth trying.

Special thanks to my mentor, Joseph Rinehart — without his guidance and the support of the USDA, I wouldn’t have been able to complete this.
