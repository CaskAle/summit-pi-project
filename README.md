# Summit-pi project

The primary focus of this document is to provide some guidance on using a Raspberry Pi as a headless device.  A headless device is one that does not have a keyboard, mouse, and display attached.  Servers, IoT devices, Networking equipment, and "_The Cloud_" are all examples of headless devices.  While primarily focussed on headless operation, the content may also prove useful to those who are using a more traditional setup. This document is located at: <https://github.com/CaskAle/summit-pi-project>
If you are not yet comfortable with linux and the command line, you may want to read the section on [Useful Linux Commands](#useful-linux-commands) before continuing.

You will very likely wish to keep these instructions active for reference while hopping to the various links referenced within.  To do this, use your browser's tab function and open multiple tabs.  Simply right click on the links and select **Open in new tab**.

## Installing the Raspberry Pi Operating System

You will be setting up your Raspberry Pi as a headless device.  You will be writing a new image of the Raspberry Pi Operating System (OS) directly to the devices SD card from your laptop.  Once the OS has been written, you will then put the SD card into the Raspberry Pi and boot it.

1. Start by using the official guide for [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up).

1. When selecting the operating system, be sure to choose the **Raspberry Pi OS Full (32-bit)** image.  Otherwise, you may not get all the necessary software installed.

   > Note: The **Raspberry Pi OS Full (32-bit)** is hidden in a sub-menu entitled **Raspberry PI OS (other)**.

1. Before pressing the `Write` button to create your image, bring up the hidden advanced configuration settings by pressing `Ctrl + Shift + X`.  In these settings, set the following items:

   - **Set hostname**.  Choose a hostname that will be unique on your network
   - **Enable SSH**. Select the password authentication method and set a password for the pi user.  You will need this later so don't forget it.
   - **Configure wifi**.  Set the SSID and Password for your local network.
   - **Wifi Country**.  Set to **US**`.
   - **Set Locale**.  Our timezone settings will be under **America**.  THe keyboard should be **us**
   - **Skip first-run wizard**
   - **Eject media when finished**.  This does not physically eject the card from the slot.  Rather, it tells the operating system to close all open files so that the card can be safely removed.
   - **Save**
1. Now you can press the `Write` button and your operating system will be written to the MicroSD card.

   > Note: The instructions in the setup guide stop being relevant to a headless install once you get to the point where they mention Raspberry Pi OS desktop appearing.  In a headless environment, you will not see the Raspberry Pi OS Desktop.  Instead, you will be connecting to the device over the network, from your laptop, via a terminal and the ssh program.
  
1. The first boot will take a few minutes as there is some initial setup that will be done.  Give it about 5 minutes on the first boot before trying to connect to it.  To verify that the device has connected to the network, use the ping command from a terminal on your laptop.  
`ping -c 5 <hostname>.local`
   > Replace `<hostname>` with the hostname you chose during image creation.

1. Once your device has connected to the network, you can connect to it, from a laptop, that is also connected to the same network.  From a terminal on your laptop and enter:  
`ssh pi@<hostname>.local`
   > Replace `<hostname>` with the hostname you chose during image creation.  The default userid on the the Raspberry Pi is **pi**.  You will be prompted for pi's password.  Use the password you chose during image creation.

1. Once you are connected, you should do a software update first thing.  To do this, enter the following commands in your terminal:

     ```bash
     sudo apt update
     sudo apt dist-upgrade -y
     sudo reboot
     ```

   > After the reboot, you will need to reconnect to the device by using the ssh command again.

1. At this point, you should have a configured Raspberry Pi that you can use for the assigned exercices.

There is a Raspberry Pi configuration tool called [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).  Many elements of the device can be configured via this tool.  Be sure to run the tool as root with the **sudo** command: `sudo raspi-config`.  When you exit the config program, if you made changes, you will be asked if you wish to reboot.  Be sure to say yes so that all your changes are activated.

## TJBot Setup Notes

Your Raspberry Pi should now be ready to start working on the TJBot Recipes

There are a few gotchas and fixes that you will need to perform on your Raspberry Pi as you go through these recipes.  Please proceed to the following document and use it as a guide for completing the recipes.

[TJBot Setup Notes](https://github.com/CaskAle/summit-pi-project/blob/master/TJBot-setup-notes.md)

## What Next

learn linux  
node-red  
python  
ai  
data science  
iot

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

---

## Other Stuff

### Setting a unique hostname

The default host name for a Raspberry Pi is **raspberrypi**. If your device is the only one on a network with that name, then all is good.  However, setting a unique hostname will be extremely useful in allowing you to connect to the device remotely.  Due to the large number of devices that may be connecting to the network, you will need a way to distinguish yours from all the others.  Decide on a unique hostname for your device.  One way to ensure this is to add your IBM employee number to the end of whatever name you choose. (for example: **tjbot-xxxxxx**).  

There are several ways to set the hostname but the first option is probably the easiest.

- `sudo hostnamectl set-hostname new-hostname`

- `sudo raspi-config`.  This tool lets you perform several other configuration changes at the same time, such as enabling ssh, change password, etc.

- `sudo nano /etc/hostname`. Editing this file will allow you to directly enter a new hostname into the file it is stored in.  You should reboot after editing in this way for the change to take effect with `sudo reboot`.

### Configuring WiFi

The WiFi configuration is stored in a file called wpa_supplicant located in the /etc/wpa_supplicant directory.  Multiple networks can be added to this file and they will be tried, in the order they appear.

- It can be very useful to configure a [mobile phone hotspot](#mobile-phone-hotspot) as the first network in this file.  This way, you always have a fallback method of connecting to the device.

- In the sample below, replace `<WiFi SSID>` and `<WiFi Password>`, (leave the quotation marks in place) with the SSID and password of your WiFi networks. **Remove any extra network definitions if they are not needed**.

- Here is a sample wpa_supplicant.conf file that can copied, modified, and used.

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
  ssid="<WiFi SSID>"
  psk="<WiFi Password>"
}

network={
  ssid="<WiFi SSID>"
  psk="<WiFi Password>"
}

network={
  ssid="<WiFi SSID>"
  psk="<WiFi Password>"
}
```

### Mobile Phone Hotspot

Using your mobile phone as a hotspot is a great way to get your Pi onto a network so that you can connect to it.  If both your laptop and the Raspberry Pi connect to the hotspot, you will be able to access the device via a terminal on your laptop.

- **Configure a hotspot on your mobile**  
The configuration of your phone's hospot, will vary by manufacturer but generally it will be in the **__Network__** section of the phone's setup menus.  Here, you will give the hotspot a name and a password.  Then, turn it on.

- Connect your laptop to the hotspot just like you would any other WiFi network.

- Follow the [Configuring WiFi](#configuring-wifi) instructions to add a network to an existing configuration.

### IBM Network Considerations

- You will likely not be able to connect the Raspberry Pi as a headless device (no keyboard or display) to the IBM WiFi network.  This is due to the fact that there is no way to enter the appropriate user and password info required to connect.

- If your site has Ethernet, it may be an option.  However, it will take some special preparations to ensure that each Raspberry Pi has a unique name.  Without a unique name, it will be very difficult to connect remotely to the device.  This is acomplished by [setting a unique hostname](#set-a-unique-hostname).

- One very helpful and recommended option inside IBM is to leverage the WiFi hotspot capability of your mobile phone to connect to your .  Check out [Mobile Phone Hotspot](#mobile-phone-hotspot) for details.
