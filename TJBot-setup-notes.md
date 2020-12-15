# TJBot Setup Notes

The instructions setting up and using the Raspberry Pi as a TJBot are found in the [README.md](https://github.com/ibmtjbot/tjbot/blob/master/bootstrap/README.md) file.  This file can be found in the "~/Desktop/tjbot/bootstrap" directory once the setup has completed.

- Use ssh to connect to your device and run the following command to begin the TJBot setup:  

   `curl -sL http://ibm.biz/tjbot-bootstrap | sudo sh -`  

   This kicks off an install script that will ask several questions.  In most cases, you should just accept the defaults.  However, in a few cases, described below, you may want to deviate from the default:

  **Hostname:**  
  As previosly mentioned, due to the large number of Raspberry Pis that will be on your network, be sure you have a unique hostname for your device.

  **Quad 9 Nameservers:**  
  The Quad 9 nameservers (9.9.9.9) are IBM owned and maintained nameservers that focus on security and privacy.  No harm in using them, your choice.

  **nodejs:**
  Answer **Y** when asked if you would like to install a newer version of Node.js.  The Node.js that is pre installed on the Raspberry Pi OS does not work properly with the latest tjbot libraries.

  **Install Location:**  
  All of the instructions and scripts for the TJBot recipes expect to find the TJBot code in the default location.  It is definitely best to accept the default here.

  **LED / Sound Conflict:**  
  You should answer **No** to this question.  Keep in mind that you will need to run the LED based recipes and the speaker based recipes separately.  The LED and the 3.5mm speaker jack do not work well together.  The resolution of this issue is documented in the [Speaker Not Working](#speaker-not-working) section.

  **Hardware Tests:**  
  **Optional but recommended**.   If you choose not to run them now, there are instructions for running them later in the [Perform Hardware Tests](#perform-hardware-tests) section.

## TJBot Special Notes

### Update your Raspberry Pi OS and Node.js

Is is important to be running the lates version Node.js (currently 15.x).  If you did not update your Node.js during the TJBot install process, you will need to do it now.  Included here will also be instructions for first performing an update of the Raspberry Pi OS.

1. Perform a full system update by executing the following two commands:

   `sudo apt update`  

   `sudo apt full-upgrade -y`  

1. Ensure that the latest Node.js is installed by executing the following 2 commands:

   `curl -sL https://deb.nodesource.com/setup_15.x | sudo bash -`  

   `apt-get install -y nodejs`

1. Reboot the Raspbery Pi:

   `sudo reboot`

1. Wait a couple minutes and you should be able to reconnect to your Raspberry Pi.


### Perform Hardware Tests

There are a few test programs in the `~/Desktop/tjbot/tests` directory.  These can be very helpful in ensuring that your led and speaker are properly set up.  As you will be making changes to code, you may run into problems where the application does not work properly.  These tests can eliminate the hardware as the source of the problem and save a lot of time.  The instructions for using the tests are found in the [README.md](https://github.com/ibmtjbot/tjbot/tree/master/tests) file located in `~/Desktop/tjbot/tests`.

### LED Wiring

Be sure to use the larger RGB LED that came packaged in a separate pack of 5.

![RGB LEDs](https://github.com/CaskAle/summit-pi-project/raw/master/images/rgb-led.jpg "RGB LEDs")

**Correct wiring of the LED is critical.  If you wire it backwards, you will burn it out.**  
If you look closely or feel along the base of the LED you will find that one side of the base has been flattened.  When wiring, be sure to have this flat spot facing to the right and you should be all good.  

![LED Wiring](https://github.com/CaskAle/summit-pi-project/raw/master/images/wiring.png "LED Wiring")

![Breadboard](https://github.com/CaskAle/summit-pi-project/raw/master/images/breadboard.jpg "Breadboard")

#### Speaker Not Working

Due to a known issue with the Raspberry Pi involving a conflict between the LED and the 3.5mm speaker output, once you initialize the LED, the speaker will no longer produce audible output.  Unfortunately, the only solution to this is to reboot the device (`sudo reboot`).  Once the reboot has finished, the speaker should work properly.  It will continue to work fine until the next time the LED is initialized.
> **Note:** This problem only exists when using the 3.5mm jack for sound.  If you happen to have a USB or Bluetooth speaker, you can use that without issue.

#### Speaker Volume

If you are having trouble with the volume level of the speaker, you can adjust to from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise the volume to 100%.  Press `esc` when you are done and it will save and exit.  Now you should be able to hear the output at a much better volume.
