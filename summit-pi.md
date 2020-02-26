# Summit-pi project notes
## Useful programs
- sudo
- nano
- ls
- cd


## Working _"Headless"_
One of the challenges faced with the Raspberry pi is the need to connect to it in what is known as a "headless" environment.  This means a device that has no keyboard or display to work from.  This issue is made even more difficult due to the, well warranted, network security restrictions of the IBM internal network.

---
### /etc/wpa_supplicant/wpa_supplicant
The WiFi configuration is stored in this file.  Multiple networks can be added to this file and they will be tried, in order.  Replace the `<text>` (leave the quotes in place) with the SSID and password of your WiFi networks.  List your mobile hotspot first and it will connect to it if it is turned on.  Otherwise, it will just skip to the next entry, and so on...

There is a sample file that can be used at 
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
### **hostname**
Setting a unique hostname will be extremely helpful in allowing you to connect to the pi remotely.  Due to the large number of pis that will be connecting to the network, you will want a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to accomlish this is to add your IBM employee number to the end. (ex. tjbot-XXXXXX).  There are several ways to acomplish this but...
- `sudo hostnamectl --set-hostname <hostname>`
- `sudo raspi-config`
- `sudo nano /etc/hostname`

---
### **ssh**

In order to connect to the pi remotely, you will need to use the ssh protocol.  If you have set a unique hostname, you should be able to connect to your pi with the command: `ssh pi@hostname.local`.  Where _hostname_ is the unique hostname of your device.  pi is the default username on the pi.  You will also need to enter the password fo the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.
- certificate


---
### **ip address**

---
### **mobile hostspot**
Using your mobile phone as a hotspot is a great way to get the Pi and your laptop connected.
- configure hotspot on phone
- edit wpa_supplicant
- can contain multiple, put hotspot first in order of preference


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
