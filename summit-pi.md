# Summit-pi project

This document is located at: <https://github.com/CaskAle/summit-pi-project>

## IBM Network Considerations

- You will likely not be able to use the IBM WiFi when running your Raspberry Pi as a headless device (no keyboard or display).  This is due to the fact that these is no way to enter the appropriate user and password info required to connect.

- If your site has Ethernet, it may be an option.  However, it will take some special preparations to ensure that each Raspberry Pi has a unique name.  Without a unique name, it will be very difficult to connect remotely to the device.  This is acomplished by [setting a unique hostname](#set-a-unique-hostname).

- One very helpful and recommended option is to leverage the WiFi hotspot capability of your mobile phone.  Check out [Mobile Phone Hotspot](#mobile-phone-hotspot) for details.

## Set up the Raspberry Pi (the easy way)

Your kit should include an SD card that is already loaded with software called [NOOBS](https://github.com/raspberrypi/noobs/blob/master/README.md) (**N**ew **O**ut **O**f **B**ox **S**oftware).  NOOBS is a tool that gets your device up and running quickly.  If you do not have these items, you will need to skip ahead to [the harder way](#but-i-don't-have-that-stuff-:frowning_face:-(the-harder-way)).

- If you have access to a USB keyboard, USB mouse, and HDMI display, using NOOBS is the quickest way to get going.  Simply procede to [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up).  

- Because you already have NOOBS, you can ignore the step that refers to setting up your SD card.

- Once you are up and running, take the time to [configure your Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-using/9).  At the very minimum, you should [set a unique hostname](#set-a-unique-hostname), enable [ssh](#ssh), change the password, and set up WiFi to a [mobile phone hotspot](#mobile-phone-hotspot) so that you can connect to the device remotely from your laptop.

- If you will always have access to the keyboard, mouse, and display, it will be useful to go through the [Using your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-using) guide.

## But I don't have that stuff :frowning_face: (the harder way)

If you do not have access to a keyboard, mouse, and display, all is not lost.  It is still possible to setup your device as a headless device.  Using this method, NOOBS is no longer an option.  Instead, you need to write a new image of the Raspbian OS directly to the SD card.  Once the OS has been written, you will need to make a few changes to the SD card before putting it into the device and booting.

1. Start by using the same [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up) instructions as listed above but, in this case, **DO NOT** skip the step that refers to setting up your SD card, **DO NOT** eject the SD card as indicated on the last step of the SD card setup, and **DO NOT** proceed to the next step, "Connect your Pi" until completing the following steps.

2. Follow the instructions in [Setting up a Raspberry Pi headless](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) and the notes below on editing files to enable a WiFi connection and ssh at boot time.

   - Editing files on the SD card from a Mac can be done by openning the "terminal" application.  From there, you should find the SD card's boot directory at `/Volumes/boot`.

   - Use the "touch" command to create an empty file called ssh in the boot directory:  
   `touch /Volumes/boot/ssh`.

   - For WiFi, use the [nano](#nano) editor to create a wpa_supplicant.conf file in the /Volumes/boot directory.  Guidance on this file can be found in the [Configuring WiFi](#configuring-wifi) section.

   - Be sure to eject the SD card properly.  **DO NOT** just remove it.  Otherwise, due to caching, there is a good chance your modifications will not be saved on the SD card.You can do this from either the desktop or from within the Finder.

3. Now you can continue with the standard Raspberry Pi setup where you left off at [Connect your Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

   > Note: The instructions in the setup guide stop being relevant to a headless install once you get to the point where you invited to power up the device.  In a headless environment, you will not see the Raspbian Desktop.  Instead, you will connect to the device over the network, from your laptop, via a terminal and ssh.
  
4. Ensure that your mobile hotspot is turned on before powering up the device.

5. Plug in your Pi.  The first boot will take a few minutes as there is some setup that will be done.  Give it about 5 minutes on the first boot before trying to connect.  Eventually, your phone should indicate the number of devices that are connected to the hotspot and you can use this as one way to verify that the Pi is ready.  You can also look for the blinking green light on the Pi that indicates WiFi data is flowing.

6. If your device is the only Raspberry Pi on the network (for instance, if you  [use your mobile phone as a hotspot](#mobile-phone-hotspot)) you should now be able to connect to it from a laptop, that is also connected to the same network, via ssh.  Open a terminal on your laptop and enter:  
`ssh pi@raspberrypi`

7. There is a Raspberry Pi configuration tool called [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).  Many elements of the device can be configured via this tool.  At the very minimum, you should set a [unique hostname](#set-a-unique-hostname), your timezone, your locale, your keyboard, and change the password.  Be sure to run the tool as root with:  
`sudo raspi-config`

### Set a unique hostname

The default host name for the device is **raspberrypi**. If your device is the only one on a network with that name, then all is good.  However, setting a unique hostname will be extremely useful in allowing you to connect to the Pi remotely.  Due to the large number of Pis that may be connecting to the network, you will need a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to ensure this is to add your IBM employee number to the end of whatever name you choose. (for example: **tjbot-xxxxxx**).  

There are several ways to set the hostname but the first option is probably the easiest.

- `sudo hostnamectl set-hostname new-hostname`

- `sudo raspi-config`.  This tool lets you perform several other configuration changes at the same time, such as enabling ssh, change password, etc.

- `sudo nano /etc/hostname`. Editing this file will allow you to directly enter a new hostname into the file it is stored in.  You should reboot after editing in this way for the change to take effect with `sudo reboot`.

### ssh

In order to connect to the Pi remotely, you will use a tool called ssh (**S**ecure **SH**ell).  If you have set a unique hostname for your device, you should be able to connect to it with the command: `ssh pi@<hostname>.local`.  Where `<hostname>` is the unique hostname of your device.  Alternatively, if you know the ip address of your device, you can connect with: `ssh pi@xxx.xxx.xxx.xxx`. The use `pi@` indicates the username that you are connecting as.  The user pi is the default user on the device.  You will also need to enter the password fo the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.  If you did not create a unique password when you first set up the pi, you can do so at any time by using the `passwd` command.

### Mobile Phone Hotspot

Using your mobile phone as a hotspot is a great way to get your Pi onto a network so that you can connect to it.  If both your laptop and the Raspberry Pi connect to the hotspot, you will be able to access the device via a terminal on your laptop.

- **Configure a hotspot on your mobile**  
The configuration of your phone's hospot, will vary by manufacturer but generally it will be in the **__Network__** section of the phone's setup menus.  Here, you will give the hotspot a name and a password.  Then, turn it on.

- Connect your laptop to the hotspot just like you would any other WiFi network.

- Follow the [Configuring WiFi](#configuring-wifi) instructions below to create an intial configuration in a headless environment or to add a network to an existing configuration.

### Configuring WiFi

The WiFi configuration is stored in a file called wpa_supplicant located in the /etc/wpa_supplicant directory.  Multiple networks can be added to this file and they will be tried, in the order they appear.

- If you place a wpa_supplicant file into the /boot directory of the Raspberry Pi /boot directory before booting it up the first time, that file will be automatically moved to the /etc/wpa_supplicant directory at first boot.  It will then attempt to connect to the WiFi networks specified in the file, in the order they appear.

- It is very useful to configure a [mobile phone hotspot](#mobile-phone-hotspot) as the first network in this file.  This way, you always have a fallback method of connecting to the device.

- In the sample below, replace the the text and brackets <>, (leaving the quotation marks in place) with the SSID and password of your WiFi networks. **Remove any extra network definitions if they are not needed**.

- Here is a sample [wpa_supplicant.conf](<https://github.com/CaskAle/summit-pi-project/blob/master/wpa_supplicant.conf>) file that can copied, modified, and used.

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

## Useful Linux Commands

The vast majority of your work on the device will be done via a terminal.  While this list is far from complete, here are some useful commands that will help you to navigate the linux.

### man 
The linux help system. When dealing with the linux command line, the `man` command may be your best friend.  Most command line tools contain many flags and arguments that can be added to the command to modify the way the command works.  There can be so many of these flags that is becomes impossible to remember them all. Just check out `man ls` the simple command for listing a directory contents for an example of how many flags a command might have.  To use man, just enter the command followed by the command you want help with.  For example: `man systemctl` will bring up the man pages for the systemctl command.  
> Note: Type the letter q to quit man when you are finished.

### sudo

A program that allows users to run programs with the security privileges of another user, by default the superuser (root). It gets its name from "superuser do" as it was designed to run commands as the superuser.  The user pi is already authorized to use the sudo command so any command that you would like to execute as the root user just needs to be prefixed with sudo: `sudo some-command`.  You will then need to enter the password for the pi user.

### nano

 A text editor that uses a command line interface.  It is ideal for editing configuration files when accessing a pi remotely via ssh.  To edit a file with nano simply use the nano command followed by a file name:  
`nano /dir1/dir2/filename`  
> **Note:** Editing system files on the Pi will also require the [sudo](#sudo) command.

When done editing the file, enter `ctrl-x` and than answer yes or no when asked if you would like to save the file.

### ls

A command to list files in a directory.

- `ls` by itself will list the contents of the current directory.

- `ls dir1/dir2` will list the contents of the /dir1/dir2 directory.

- There are several flags that can be used to modify the output of the ls command but 2 very common ones are -a and -l.
  
  - `ls -a /dir` will include hidded files.  Hidden files in linux begin with a dot and are sometimes called dotfiles.

  - `ls -l /dir` produces a long listing that gives greater detail about the files.

  - Flags can also be combined: `ls -al`.

### cd

 A command to change the working directory.  

- `cd /dir` will change to the /dir directory.

- To quickly change to directories within your home directory (/home/pi), you can use the ~ shortcut for the home directory. For example, to change to the /home/pi/tjbot directory, enter: `cd ~/tjbot`.

- One final shortcut.  If you find yourself regularly changing between two directories, you can use the `cd -` shortcut.  This simply alternates between the last two directories you have had as the working directory.

### ip

A command to display and set various ip network settings.
- `ip addr` will show your ip address info.

- `ip route` will show ip routing info.

### Reboot / Shutdown

- `sudo reboot` will reboot the Pi.

- `sudo shutdown now` will shut it down.  Before unplugging your Pi, you should shut it down.  Shutdown takes about 30 seconds to complete.  You can tell it is done when the gree LED on the Pi stops blinking and just stays off.

### System updates
You should occasionally check for and apply system updates to ensure that bugs and security vulnerabilities are addressed.  This is done by executing the following 2 commands:  
`sudo apt update`  
`sudo apt upgrade`

---

## TJBot Setup Notes

1. From a terminal, run the following command to begin the TJBot installation:  
`curl -sL http://ibm.biz/tjbot-bootstrap | sudo sh -`

2. The install script will ask several questions.  Some guidance on on the correct responses follows:

   - **Hostname**:  
    Due to the large number of devices that will be in play, decide on another hostname for your device.  Make sure that it is unique to you.  One way to acomlish this could be to add your IBM employee number to the end. (ex. tjbot-439321)

   - **Quad 9 Nameservers**:  
    The Quad 9 nameservers (9.9.9.9) are IBM owned and maintained nameservers that focus on security and privacy.  No harm in using them, your choice.

   - **Camera**:  
    The Summit kit does not include a camera.  If you get a camera later, it is very easy to add the camera support at that time wih the `sudo raspi-config` command.

   - **Install Location**:  
    Many of the tjbot recipe instructions and scripts are written assuming that the default location for  the TJBot code (/home/pi/Desktop/tjbot) is chosen.  While not required, I suggest keeping the clone location to the default of `/home/pi/Desktop/tjbot`.

   - **LED / Sound Conflict**:  
    On the later models of Raspberry Pi, it no longer appears to be necessary to disable the kernel sound modules in order for the LED to work.  You should answer **No** to this question.  However, the LED and the speaker still have issues working well together.  The resolution of this will be discussed in a later section.

   - **Do not run the Hardware tests when asked**:  
    Due to the LED/Speaker issue as well as a known bug with the Raspberry Pi 4, running the hardware tests at this point is an exercise in futility.  Instead look below for the section on [Hardware Testing](#hardware-testing).

---

## TJBot recipe Issues

We made the decision to provide you with the most recent version of the Raspberry Pi (version 4) as they are much higher performing.  However, the TJBot recipes were designed with an older Raspberry Pi in mind.  As a result, there are a couple of places where you will need to apply a patch to the code in order for the recipes to work.

### Hardware Testing
There are a few programs in the "tjbot/bootstrap/tests" directory.  These can be very helpful in ensuring that your led and speaker are properly set up.  As you will be making changes to code, you may run into problems where the application does not work properly.  These tests can eliminate the hardware as the source of the problem and save a lot of time.  THe instructions for using the tests are found in the [README.md](https://github.com/ibmtjbot/tjbot/blob/master/bootstrap/README.md) file located in "tjbot/bootstrap".
> Note: The LED test will require patching before use.  See: [Raspberry Pi 4 LED Bug](#raspberry-pi-4-led-bug).

### Raspberry Pi 4 LED Bug

In all three of the recipe instructions you will come to a spot where you are instructed to run the command: `npm install`.  At that point, execute the following six commands.  This will patch the LED Pi 4 bug and allow the LED to work properly.

1. Be sure that you are in the appropriate directory for the specific recipe or test you are working on:  
`cd ~/Desktop/tjbot/bootstrap/tests`  
`cd ~/Desktop/tjbot/recipes/conversation`  
`cd ~/Desktop/tjbot/recipes/sentiment_analysis`  
`cd ~/Desktop/tjbot/recipes/speech_to_text`

2. `npm install`

3. `npm install rpi-ws281x-native@latest`

4. `git clone --single-branch --branch raspi4support https://github.com/jimbotel/rpi_ws281x.git`

5. `cp -r rpi_ws281x/* node_modules/rpi-ws281x-native/src/rpi_ws281x`

6. `npm build node_modules/rpi-ws281x-native`

7. Now, you may proceed with the recipe where you left off.

> **Note: you will need to repeat these steps for each of the TJBot recipes.**

### Conversation Recipe Issues

- The ~~conversation workspace ID~~ that you are asked to make note of is now called a **_Skill ID_**.  You can find it by clicking the 3 dot menu to the right of the dialog you just imported.

- Due to the issue described in the [Speaker Not Working](#speaker-not-working) section, below.  Ensure that the LED does not get initialized. javascript code in the conversation.js file.  In order to prevent the LED from initializing is to edit the conversation.js file.  In that file, at or near line 26, you should see the following code:

```javascript
// these are the hardware capabilities that TJ needs for this recipe
var hardware = ['microphone', 'speaker', 'led', 'servo', 'camera'];
if (config.hasCamera == false) {
    hardware = ['microphone', 'speaker', 'led', 'servo'];
}
```

You need to remove the two references to the LED that look like this: **`'led',`**.  Be sure to remove the the tick marks, the word led, and the trailing comma as well.  Save the file and you are ready to go.  If you do not do this, you will just get a bunch of garbled noise.

#### Speaker Not Working

Due to a known issue with the Raspberry Pi involving a conflict between the LED and the 3.5mm speaker output, once you initialize the LED, the speaker will no longer produce audible output.  Unfortunately, the only solution to this is to reboot the device. `sudo reboot`.  Once the reboot has finished, the speaker should work properly.  It will continue to work fine until the LED is initialized.
> Note: This problem only exists when using the 3.5mm jack for sound.  If you happen to have a USB or Bluetooth speaker, you can use that without issue.

#### Speaker Volume

If you are having trouble with the volume level of the speaker, you can adjust to from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise the volume to 100%.  Press `esc` when you are done and it will save and exit.  Now you should be able to hear.

## What Next

learn linux  
node-red  
python  
ai  
data science  
