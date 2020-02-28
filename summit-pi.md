# Summit-pi project

<https://github.com/CaskAle/summit-pi-project>

## Set up the Raspberry Pi

Using NOOBS: <https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up>

Direct OS Image Install (useful for 100% headless install.  No keyboard/mouse/display): <https://www.raspberrypi.org/documentation/installation/installing-images/README.md>

## Useful Commands

The vast majority of your work on the Raspberry Pi will be done via a command line.  While this list is far from complete, here are some useful commands that will help you to navigate the system.

- **sudo** - A program that allows users to run programs with the security privileges of another user, by default the superuser (root). It gets its name from "superuser do" as it was designed to run commands as the superuser.  The user pi is already authorized to use the sudo command so any command that you would like to execute as the root user just needs to be prefixed with sudo: `sudo some-command`.  You will then need to enter the password for the pi user.  See: `man sudo` for more detail.

- **nano** - A text editor that uses a command line interface.  It is ideal for editing configuration files when accessing a pi remotely via ssh.  To edit a file with nano simply use the nano command followed by a file name:  
`nano /dir1/dir2/filename`.  **Note:** Most system configuration files on the pi will also require the `sudo` command.  When done editing the file, simply enter `ctrl-x` and answer yes or no when asked if you would like to save the file.  See: `man nano` for more detail.

- **ls** - A command to list files in a directory.  `ls` by itself will list the contents of the current directory. `ls dir1/dir2` will list the contents of the /dir1/dir2 directory.  There are several flags that can be used to modify the output of the ls command but dir2 very common ones are -a and -l.  the -a flag `ls -a /dir` will include hidded files.  Hidden files in linux begin with a dot and are sometimes called dotfiles.  The -l flag `ls -l /dir` produces a long listing that gives greated detail about the files.  The flags can also be combined `ls -al`.

- **cd** - A command to change the working directory.  Issue the command `cd /dir` will change to the /dir directory.  To quickly change to directories within your home directory (/home/pi), you can use the ~ shortcut for the home directory. For example, to change to the /home/pi/tjbot directory, enter: `cd ~/tjbot`.  One final shortcut.  If you find yourself regularly changing between two directories, you can use the `cd -` shortcut.  This simply alternates between the last two directories you have had as the working directory.

## Working "Headless"

One of the challenges faced with the Raspberry pi is the need to connect to it in what is known as a "headless" environment.  This means a device that has no keyboard or display to work from.  This issue is made even more difficult due to the, well warranted, network security restrictions of the IBM internal network.

### Set a unique hostname

Setting a unique hostname will be extremely helpful in allowing you to connect to the pi remotely.  Due to the large number of pis that will be connecting to the network, you will want a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to accomlish this is to add your IBM employee number to the end. (ex. tjbot-XXXXXX).  There are several ways to acomplish this but the first option is probably the easiest.

- `sudo hostnamectl set-hostname <hostname>`

- `sudo raspi-config` This tool lets you perform several other configuration changes at the same time, such as enabling ssh.

- `sudo nano /etc/hostname` Editing this file will allow you to diectly enter a new hostname into the file it is stored in.  You should reboot after editing in this way.  `sudo reboot`.

### ssh

In order to connect to the pi remotely, you will use a tool called ssh (**S**ecure **SH**ell).  If you have set a unique hostname for your Raspberry Pi, you should be able to connect to it with the command: `ssh pi@<hostname>.local`.  Where `<hostname>` is the unique hostname of your device.  Alternatively, if you know the ip address of your Raspberry Pi, you can connect with: `ssh pi@xxx.xxx.xxx.xxx`. The use `pi@` indicates the username that you are connecting as.  The user pi is the default user on the Raspberry Pi.  You will also need to enter the password fo the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.  If you did not create a unique password when you first set up the pi, you can do so at any time by using the `passwd` command.

### Use a mobile phone hostspot

Using your mobile phone as a hotspot is a great way to get your pi onto a network so that you can connect to it.

- **Configure a hotspot on your mobile**  
The location in the setup menu for will vary by device but generally it will be in the **__Network__** section of the setup menus.  Here, you will give the hotspot a name and a password.

- **Edit `/etc/wpa_supplicant/wpa_supplicant` on your pi**  
The WiFi configuration is stored in this file.  Multiple networks can be added to this file and they will be tried, in order.  Replace the `<text>` (leave the quotes in place) with the SSID and password of your WiFi networks.  List your mobile hotspot first and it will connect to it if it is turned on.  Otherwise, it will just skip to the next entry, and so on...  There is a sample file that can be used at <https://github.com/CaskAle/summit-pi-project.>

```bash
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

## TJBot Setup Notes

1. Boot the Raspberry Pi and make sure it is connected to the network. There is an icon in the menu bar at the top of the screen for connecting to a wifi network.

2. Open the Terminal (black square icon with the ">\_" at the top of the screen).

3. Run the following command to begin the TJBot installation.  
`curl -sL http://ibm.biz/tjbot-bootstrap | sudo sh -`

### Hostname

Due to the large number of devices that will be in play, decide on another hostname for your device.  Make sure that it is unique to you.  One way to acomlish this could be to add your IBM employee number to the end. (ex. tjbot-439321)

### Quad 9 Nameservers

The Quad 9 nameservers (9.9.9.9) are IBM owned and maintained nameservers that focus on security and privacy.  No harm in using them, your choice

### Camera

The Summit kit does not include a camera.  If you get a camera later, it is very easy to add the camera support at that time wih the `sudo raspi-config` command.

### Install Location

Many of the tjbot recipe instructions and scripts are written assuming that the default location for  the TJBot code (/home/pi/Desktop/tjbot) is chosen.  While not required, I suggest keeping the clone location to the default of `/home/pi/Desktop/tjbot`.

### LED / Sound Conflict

On the later models of Raspberry Pi, it no longer appears to be necessary to disable the kernel sound modules in order for the LED to work.  You should answer **No** to this question.  However, the LED and the speaker still have issues working well together.  The resolution of this will be discussed in a later section.

### Do not run the Hardware tests when asked

Due to the LED/Speaker issue as well as a known bug with the Raspberry Pi 4, running the hardware tests at this point is an exercise in futility.  Instead look below for the section on **Hardware Testing**.

### Watson Credentials

Before running any recipes, you will need to obtain credentials for the Watson services used by those recipes.  You can obtain these credentials as follows:

1. Sign up for a free IBM Cloud account at <https://cloud.ibm.com> if you do not have one already.

2. Log in to IBM Cloud and create an instance of the Watson services you plan to use. The Watson services are listed on the IBM Cloud dashboard, under "Catalog". The full list of Watson services used by TJBot are: **Assistant, Language Translator, Speech to Text, Text to Speech, Tone Analyzer, and Visual Recognition**

3. For each Watson service, click the "Create" button on the bottom right of the page to create an instance of the service.

4. Click "Service Credentials" in the left-hand sidebar. Next, click "View Credentials" under the Actions menu.

5. Make note of the credentials for each Watson service. You will need to save these in the config.js files for each recipe you wish to run. For more detailed guides on setting up service credentials, please see the README file of each recipe, or search instructables.com for "tjbot".

---

## Running the TJBot recipes

### Pi 4 LED Bug

In the recipe instructions you will come to a spot where you are instructed to run the command: `npm install`.  At that point, execute the following six commands.  This will patch the LED Pi 4 bug and allow the LED to work properly.

- Be sure that you are in the appropriate directory for the specific recipes or tests:  
`cd ~/Desktop/tjbot/bootstrap/tests`  
`cd ~/Desktop/tjbot/recipes/conversation`  
`cd ~/Desktop/tjbot/recipes/sentiment_analysis`  
`cd ~/Desktop/tjbot/recipes/speech_to_text`

- `npm install`

- `npm install rpi-ws281x-native@latest`

- `git clone --single-branch --branch raspi4support https://github.com/jimbotel/rpi_ws281x.git`

- `cp -r rpi_ws281x/* node_modules/rpi-ws281x-native/src/rpi_ws281x`

- `npm build node_modules/rpi-ws281x-native`

Now, you may proceed with the recipe as normal.
> **Note: you will need to repeat these steps for each of the TJBot recipes.**

## Speaker Volume

If you are having trouble with the volume level of the speaker, you can adjust to from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise the volume to 100%.  Press `Esc` when you are done and it will save and exit.  Now you should be able to hear.
