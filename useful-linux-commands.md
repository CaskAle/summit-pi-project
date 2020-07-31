# Useful Linux Commands

The vast majority of your work on the device will be done via a terminal and the ssh program.  While this list is far from complete, here are some useful commands that will help you to navigate the linux command line.

## man

The linux help system. When dealing with the linux command line, the `man` command may be your best friend.  Most command line tools contain many flags and arguments that can be added to the command to modify the way the command works.  There can be so many of these flags that is becomes impossible to remember them all. Just check out `man ls` the simple command for listing a directory contents for an example of how many flags a command might have.  To use man, just enter the command followed by the command you want help with.  For example: `man systemctl` will bring up the man pages for the systemctl command.  
> Note: Type the letter q to quit man when you are finished.

## ssh

In order to connect to the Pi remotely, you will use a tool called ssh (**S**ecure **SH**ell).  If you have set a unique hostname for your device, you should be able to connect to it with the command: `ssh pi@<hostname>.local`.  Where `<hostname>` is the unique hostname of your device.  Alternatively, if you know the ip address of your device, you can connect with: `ssh pi@xxx.xxx.xxx.xxx`. The use `pi@` indicates the username that you are connecting as.  The user pi is the default user on the device.  You will also need to enter the password for the user pi.  At this point it should become obvious that it is also critical that a unique password be set for your default user, pi.  If you did not create a unique password when you first set up the device, you can do so at any time by using the `passwd` command.

## sudo

A program that allows users to run programs with the security privileges of another user, by default the superuser (root). It gets its name from "superuser do" as it was designed to run commands as the superuser.  The user pi is already authorized to use the sudo command so any command that you would like to execute as the root user just needs to be prefixed with sudo: `sudo some-command`.  You will then need to enter the password for the pi user.

## nano

 A text editor that uses a command line interface.  It is ideal for editing configuration files from a terminal.  To edit a file with nano simply use the nano command followed by a file name:  
`nano some-filename`  
> **Note:** When editing system files on the Raspberry Pi will also require the [sudo](#sudo) command.

When done editing the file, enter `ctrl-x` and than answer yes or no when asked if you would like to save the file.

## ls

A command to list files in a directory.

- `ls` by itself will list the contents of the current directory.

- `ls dir1/dir2` will list the contents of the /dir1/dir2 directory.

- There are several flags that can be used to modify the output of the ls command but 2 very common ones are -a and -l.
  
  - `ls -a /dir` will include hidded files.  Hidden files in linux begin with a dot and are sometimes called dotfiles.

  - `ls -l /dir` produces a long listing that gives greater detail about the files.

  - Flags can also be combined: `ls -al`.

## cd

 A command to change the working directory.  

- `cd /dir` will change to the /dir directory.

- To quickly change to directories within your home directory (/home/pi), you can use the ~ shortcut for the home directory. For example, to change to the /home/pi/tjbot directory, enter: `cd ~/tjbot`.

- One final shortcut.  If you find yourself regularly changing between two directories, you can use the `cd -` shortcut.  This simply alternates between the last two directories you have had as the working directory.

## ip

A command to display and set various ip network settings.

- `ip addr` will show your ip address info.

- `ip route` will show ip routing info.

## Reboot / Shutdown

- `sudo reboot` will reboot the Pi.

- `sudo shutdown now` will shut it down.  Before unplugging your Pi, you should shut it down.  Shutdown takes about 30 seconds to complete.  You can tell it is done when the gree LED on the Pi stops blinking and just stays off.

## System updates

You should occasionally check for and apply system updates to ensure that bugs and security vulnerabilities are addressed.  This is done by executing the following 2 commands:  
`sudo apt update`  
`sudo apt upgrade`
