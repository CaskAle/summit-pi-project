# Summit-pi project
https://github.com/CaskAle/summit-pi-project

## Set up the Raspberry Pi
Using NOOBS: https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up

Direct OS Image Install: (useful for 100% headless install.  No keyboard/mouse/display) https://www.raspberrypi.org/documentation/installation/installing-images/README.md

## Useful Commands
The vast majority of your work on the Raspberry Pi will be done via a command line.  While this list is far from complete, here are some useful commands that will help you to navigate the system.
- **sudo** - A program that allows users to run programs with the security privileges of another user, by default the superuser (root). It gets its name from "superuser do" as it was designed to run commands as the superuser.  The user pi is already authorized to use the sudo command so any command that you would like to execute as the root user just needs to be prefixed with sudo: `sudo some-command`.  You will then need to enter the password for the pi user.  See: `man sudo` for more detail.
- **nano** - A text editor that uses a command line interface.  It is ideal for editing configuration files when accessing a pi remotely via ssh.  To edit a file with nano simply use the nano command followed by a file name:  
`nano /dir1/dir2/filename`.  **Note:** Most system configuration files on the pi will also require the `sudo` command.  When done editing the file, simply enter `ctrl-x` and answer yes or no when asked if you would like to save the file.  See: `man nano` for more detail. 
- **ls** - A command to list files in a directory.  `ls` by itself will list the contents of the current directory. `ls dir1/dir2` will list the contents of the /dir1/dir2 directory.  There are several flags that can be used to modify the output of the ls command but dir2 very common ones are -a and -l.  the -a flag `ls -a /dir` will include hidded files.  Hidden files in linux begin with a dot and are sometimes called dotfiles.  The -l flag `ls -l /dir` produces a long listing that gives greated detail about the files.  The flags can also be combined `ls -al`.
- **cd** - A command to change the working directory.  Issue the command `cd /dir` will change to the /dir directory.  To quickly change to directories within your home directory (/home/pi), you can use the ~ shortcut for the home directory. For example, to change to the /home/pi/tjbot directory, enter:  
`cd ~/tjbot`.  One final shortcut.  If you find yourself regularly changing between two directories, you can use the `cd -` shortcut.  This simply alternates between the last two directories you have had as the working directory.


## Working "Headless"
One of the challenges faced with the Raspberry pi is the need to connect to it in what is known as a "headless" environment.  This means a device that has no keyboard or display to work from.  This issue is made even more difficult due to the, well warranted, network security restrictions of the IBM internal network.

---
### **Set a unique hostname**
Setting a unique hostname will be extremely helpful in allowing you to connect to the pi remotely.  Due to the large number of pis that will be connecting to the network, you will want a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to accomlish this is to add your IBM employee number to the end. (ex. tjbot-XXXXXX).  There are several ways to acomplish this but the first option is probably the easiest.
- `sudo hostnamectl set-hostname <hostname>`
- `sudo raspi-config` This tool lets you perform several other configuration changes at the same time, such as enabling ssh.
- `sudo nano /etc/hostname` Editing this file will allow you to diectly enter a new hostname into the file it is stored in.  You should reboot after editing in this way.  `sudo reboot`

---
### **ssh**

In order to connect to the pi remotely, you will use a tool called ssh (**S**ecure _**SH**_ell).  If you have set a unique hostname, you should be able to connect to your pi with the command:  
`ssh pi@<hostname>.local`  Where `<hostname>` is the unique hostname of your devic**e.  Alternatively, if you know the ip address of your Raspberry Pi, you can connect with:  
`ssh pi@xxx.xxx.xxx.xxx`. The use `pi@` indicates the username that you are connecting as.  The user pi is the default user on the Raspberry Pi.  You will also need to enter the password fo the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.

---
### **Connect to a mobile hostspot**
Using your mobile phone as a hotspot is a great way to get your pi onto a network so that you can connect to it.
- **Configure a hotspot on your mobile**  
The location in the setup menu for will vary by device but generally it will be in the **__Network__** section of the setup menus.  Here, you will give the hotspot a name and a password.
- **Edit `/etc/wpa_supplicant/wpa_supplicant` on your pi**  
The WiFi configuration is stored in this file.  Multiple networks can be added to this file and they will be tried, in order.  Replace the `<text>` (leave the quotes in place) with the SSID and password of your WiFi networks.  List your mobile hotspot first and it will connect to it if it is turned on.  Otherwise, it will just skip to the next entry, and so on...  There is a sample file that can be used at https://github.com/CaskAle/summit-pi-project.
```
# /etc/wpa_supplicant/wpa_supplicant.conf

# Multiple networks can be added to this file and they will be tried, in order.
# Replace the <text> (leave the quotes in place) with the SSID and password of your
# WiFi networks.  List your mobile hotspot first and it will be connected to if it is
# turned on.  Otherwise, it will just skip to the next entry, and so on.

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
	ssid="<Mobile hotpot name>"
	psk="<Mobile hotpot password>"

network={
	ssid="<Home WiFi name>"
	psk="<Home WiFi password>"

network={
	ssid="<Another WiFi name>"
	psk="<Another Wifi password>"
}
```

---
## TJBot Automated Setup
1. Boot the Raspberry Pi and make sure it is connected to the network. There is an icon in the menu bar at the top of the screen for connecting to a wifi network.

2. Open the Terminal (black square icon with the ">\_" at the top of the screen).

3. Run the following command to begin the TJBot installation.

```
curl -sL http://ibm.biz/tjbot-bootstrap | sudo sh -
```

> Note that this script requires root access and must be run with `sudo`.  See below for a description of sudo.

**Hostname**  
Due to the large number of devices that will be in play, decide on another hostname for your device.  Make sure that it is unique to you.  One way to acomlish this could be to add your IBM employee number to the end. (ex. tjbot-XXXXXX)

**Quad 9 Nameservers**  
The Quad 9 nameservers (9.9.9.9) are IBM owned and maintained nameservers that focus on security and privacy.  _No harm in using them._

**Camera**  
The Summit kit does not include a camera.  If you get a camera later, it is very easy to add the camera support at that time wih the `sudo raspi-config` command.

**Install Location**  
The default clone location for  the TJBot code (/home/pi/Desktop/tjbot) is **NOT** optimal when the Pi will be used as a headless device.  While not required, I suggest changing the clone location to `/home/pi/tjbot`.  This is just removing the _"Desktop"_ sub-directory portion of the default to allow the code to clone straight into your home directory.

# LED / Sound Conflict
On the Raspberry Pi, there is a known conflict between the LED used in the TJBot and the built-in audio jack. In order for the LED to work, certain kernel modules need to be disabled to avoid this conflict. If you have a speaker that uses  HDMI, USB, or Bluetooth, there is no issue and there is no need to disable the sound modules.

However, if you are using the speaker that came with the Summit kit, will need to disable the sound modules when doing recipes that use the LED.  There are simple commands to execute that allow the enabling/disabling of the sound modules at any time.  
**To disable:**  
`cp ~/tjbot/bootstrap/tjbot-blacklist-snd.conf /etc/modprobe.d/`  
**To Enable:**  
`rm /etc/modprobe.d/tjbot-blacklist-snd.conf`  
**Reboot:**  
After executing either of these commands, it is necessary to reboot the Raspberry Pi to make the change take effect.  
`sudo reboot`

# Hardware Tests
TJBot includes a set of hardware tests to ensure all of the hardware is functioning properly. If you have made any changes to the camera or sound configuration, we recommend rebooting first before running these tests as they may fail. You can run these tests at anytime by running the runTests.sh script in the tjbot/bootstrap folder.

# SSH

# Watson Credentials
Before running any recipes, you will need to obtain credentials for the Watson services used by those recipes.  You can obtain these credentials as follows:

1. Sign up for a free IBM Cloud account at https://cloud.ibm.com if you do not have one already.

2. Log in to IBM Cloud and create an instance of the Watson services you plan to use. The Watson services are listed on the IBM Cloud dashboard, under "Catalog". The full list of Watson services used by TJBot are: **Assistant, Language Translator, Speech to Text, Text to Speech, Tone Analyzer, and Visual Recognition**

3. For each Watson service, click the "Create" button on the bottom right of the page to create an instance of the service.

4. Click "Service Credentials" in the left-hand sidebar. Next, click "View Credentials" under the Actions menu.

5. Make note of the credentials for each Watson service. You will need to save these in the config.js files for each recipe you wish to run. For more detailed guides on setting up service credentials, please see the README file of each recipe, or search instructables.com for "tjbot".

# VS Code
# Pi 4 LED Bug
