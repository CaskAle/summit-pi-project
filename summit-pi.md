# Summit-pi project

The primary focus of this document is to provide some guidance on using a Raspberry Pi as a headless device.  A headless device is one that does not have a keyboard, mouse, and display attached.  Servers, IoT devices, Networking equipment, and "_The Cloud_" are all examples of headless devices.  While primarily focussed on headless operation, the content may also prove useful to those who are using a more traditional setup. This document is located at: <https://github.com/CaskAle/summit-pi-project>
If you are not yet comfortable with linux and the command line, you may want to read the section on [Useful Linux Commands](#useful-linux-commands) before continuing.

You will very likely wish to keep these instructions active for reference while hopping to the various links referenced within.  To do this, use your browser's tab function and open multiple tabs.  Simply right click on the links and select **Open in new tab**.

## IBM Network Considerations

- You will likely not be able to connect the Raspberry Pi as a headless device (no keyboard or display) to the IBM WiFi network.  This is due to the fact that there is no way to enter the appropriate user and password info required to connect.

- If your site has Ethernet, it may be an option.  However, it will take some special preparations to ensure that each Raspberry Pi has a unique name.  Without a unique name, it will be very difficult to connect remotely to the device.  This is acomplished by [setting a unique hostname](#set-a-unique-hostname).

- One very helpful and recommended option inside IBM is to leverage the WiFi hotspot capability of your mobile phone to connect to your .  Check out [Mobile Phone Hotspot](#mobile-phone-hotspot) for details.

## Set up the Raspberry Pi (the easy way :smile:)

Your kit should include an SD card that is already loaded with software called [NOOBS](https://github.com/raspberrypi/noobs/blob/master/README.md) (**N**ew **O**ut **O**f **B**ox **S**oftware).  NOOBS is a tool that gets your device up and running quickly.  If you do not have these items, you will need to skip ahead to: [But I don't have all that stuff :frowning_face:](#but-i-don't-have-that-stuff-:frowning_face:-(the-harder-way)).

- If you have access to a USB keyboard, USB mouse, and HDMI display, using NOOBS is the quickest way to get going.  Simply procede to [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up) and follow the instructions there.

- Because you already have NOOBS, you can ignore the step that refers to setting up your SD card.

- During the NOOBS initial setup, be sure to choose the **Raspbian Full** image.  Otherwise, you may not get all the necessary software installed.

- Once you are up and running, take the time to [configure your Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-using/9).  At the very minimum, you should [set a unique hostname](#set-a-unique-hostname), enable [ssh](#ssh), change the password, and set up WiFi to a [mobile phone hotspot](#mobile-phone-hotspot) so that you can connect to the device remotely from your laptop.

- If you will always have access to the keyboard, mouse, and display, it may be useful to go through the [Using your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-using) guide.

## But I don't have all that stuff (the harder way :frowning_face:)

If you do not have access to a keyboard, mouse, and display, all is not lost.  It is still possible to setup your Raspberry Pi as a headless device.  Using this method, NOOBS is no longer an option.  Instead, you need to write a new image of the Raspbian OS directly to the SD card.  Once the OS has been written, you will need to make a few more changes to the SD card image before putting it into the device and booting.

1. Start by using the same [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up) instructions as listed above.

2. When creating your image, be sure to choose the **Raspbian Full** image.  Otherwise, you may not get all the necessary software installed.

3. **STOP once you have completed the Set up your SD card** step and, at that point, complete the following steps.

4. Read the short article on [Setting up a Raspberry Pi headless](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md).  Don't actually perform any of the step in the article.  Instead, return here and complete the steps below to enable a WiFi connection and ssh on your Pi.

   - Unplug the SD card from your laptop, wait a few seconds and then plug it back in.  This will make the laptop rediscover the new file systems that have been created on the card.

   - Editing files on the SD card from a Mac can be done by opening the "terminal" application on the Mac.  From there, you should find the SD card's boot directory at `/Volumes/boot`.  Use `cd /Volumes/boot` to get there.  If there does not seem to be a boot directory, repeat step 3 above.

   - To enable ssh on the Pi, use the touch command to create an empty file called "ssh" in the /Volumes/boot directory:  
   `touch /Volumes/boot/ssh`

   - To enable WiFi on the Pi, use the [nano](#nano) text editor to create a wpa_supplicant.conf file in the /Volumes/boot directory.  
   `nano /Volumes/boot/wpa_supplicant.conf`  
   Guidance on the contents of this file can be found in the [Configuring WiFi](#configuring-wifi) section of this document.

   - **Be sure to eject the SD card properly.  DO NOT just remove it**.  Otherwise, due to caching, there is a good chance your modifications will not be saved on the SD card.  You can do this from either the desktop or from within the Finder application.

5. Now you can continue with the standard Raspberry Pi setup where you left off at [Connect your Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).  

> Note: The instructions in the setup guide stop being relevant to a headless install once you complete this step.  In a headless environment, you will not see the Raspbian Desktop.  Instead, you will connect to the device over the network, from your laptop, via a terminal and the ssh program.
  
6. Ensure that the WiFi network you wish to connect to is ready before powering up the device.

7. Power up your Pi by plugging the power cord into the USB-C power port on the side of the device.  The first boot will take a few minutes as there is some setup that will be done.  Give it about 5 minutes on the first boot before trying to connect to it via ssh.  If you are using your phone's hotspot, you should see the number of devices that are connected to the hotspot change.  You can use this as one way to verify that the Pi is ready to accept connections.  You can also look for the blinking green light on the Pi that indicates WiFi data is flowing.

8. If your device is the only Raspberry Pi on the network (for instance, if you are [using your phone as a hotspot](#mobile-phone-hotspot)) you should now be able to connect to it from a laptop, that is also connected to the same network, via ssh.  Open a terminal on your laptop and enter:  
`ssh pi@raspberrypi`

9. There is a Raspberry Pi configuration tool called [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).  Many elements of the device can be configured via this tool.  At the very minimum, you should set a [unique hostname](#set-a-unique-hostname), your timezone, your locale, your keyboard, and change the password.  Be sure to run the tool as root with:  `sudo raspi-config`.  After you make these changes, be sure to select the option to reboot the Pi when you exit the raspi-config program.

10. At this point, you should have a configured Raspberry Pi that you can use for the assigned exercices.  You can connect to your Pi using ssh with its new hostname using the command: `ssh pi@my-pi-hostname`.  This will also connect you using the default userid on the the Pi (pi) so you will also need to enter the password for the pi user.  The default password for the user pi is `raspberry`.  If you did not create a unique password when you set up the device, you can do so at any time by using the `passwd` command from a terminal.

## What Next

learn linux  
node-red  
python  
ai  
data science  
iot

---

## Set a unique hostname

The default host name for the device is **raspberrypi**. If your device is the only one on a network with that name, then all is good.  However, setting a unique hostname will be extremely useful in allowing you to connect to the Pi remotely.  Due to the large number of Pis that may be connecting to the network, you will need a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to ensure this is to add your IBM employee number to the end of whatever name you choose. (for example: **tjbot-xxxxxx**).  

There are several ways to set the hostname but the first option is probably the easiest.

- `sudo hostnamectl set-hostname new-hostname`

- `sudo raspi-config`.  This tool lets you perform several other configuration changes at the same time, such as enabling ssh, change password, etc.

- `sudo nano /etc/hostname`. Editing this file will allow you to directly enter a new hostname into the file it is stored in.  You should reboot after editing in this way for the change to take effect with `sudo reboot`.

## Mobile Phone Hotspot

Using your mobile phone as a hotspot is a great way to get your Pi onto a network so that you can connect to it.  If both your laptop and the Raspberry Pi connect to the hotspot, you will be able to access the device via a terminal on your laptop.

- **Configure a hotspot on your mobile**  
The configuration of your phone's hospot, will vary by manufacturer but generally it will be in the **__Network__** section of the phone's setup menus.  Here, you will give the hotspot a name and a password.  Then, turn it on.

- Connect your laptop to the hotspot just like you would any other WiFi network.

- Follow the [Configuring WiFi](#configuring-wifi) instructions below to create an initial configuration in a headless environment or to add a network to an existing configuration.

## Configuring WiFi

The WiFi configuration is stored in a file called wpa_supplicant located in the /etc/wpa_supplicant directory.  Multiple networks can be added to this file and they will be tried, in the order they appear.

- If you place a wpa_supplicant file into the /boot directory of the Raspberry Pi /boot directory before booting it up the first time, that file will be automatically moved to the /etc/wpa_supplicant directory at first boot.  It will then attempt to connect to the WiFi networks specified in the file, in the order they appear.

- It is very useful to configure a [mobile phone hotspot](#mobile-phone-hotspot) as the first network in this file.  This way, you always have a fallback method of connecting to the device.

- In the sample below, replace the bracketted text and the brackets <>, (leaving the quotation marks in place) with the SSID and password of your WiFi networks. **Remove any extra network definitions if they are not needed**.

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
}

network={
  ssid="<Home WiFi name>"
  psk="<Home WiFi password>"
}

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

### ssh

In order to connect to the Pi remotely, you will use a tool called ssh (**S**ecure **SH**ell).  If you have set a unique hostname for your device, you should be able to connect to it with the command: `ssh pi@<hostname>.local`.  Where `<hostname>` is the unique hostname of your device.  Alternatively, if you know the ip address of your device, you can connect with: `ssh pi@xxx.xxx.xxx.xxx`. The use `pi@` indicates the username that you are connecting as.  The user pi is the default user on the device.  You will also need to enter the password for the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.  If you did not create a unique password when you first set up the device, you can do so at any time by using the `passwd` command.

### sudo

A program that allows users to run programs with the security privileges of another user, by default the superuser (root). It gets its name from "superuser do" as it was designed to run commands as the superuser.  The user pi is already authorized to use the sudo command so any command that you would like to execute as the root user just needs to be prefixed with sudo: `sudo some-command`.  You will then need to enter the password for the pi user.

### nano

 A text editor that uses a command line interface.  It is ideal for editing configuration files from a terminal.  To edit a file with nano simply use the nano command followed by a file name:  
`nano some-filename`  
> **Note:** When editing system files on the Raspberry Pi will also require the [sudo](#sudo) command.

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
