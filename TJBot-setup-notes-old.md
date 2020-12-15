# TJBot Setup Notes

The instructions setting up and using the Raspberry Pi as a TJBot are found in the [README.md](https://github.com/ibmtjbot/tjbot/blob/master/bootstrap/README.md) file.  This file can be found in the "~/Desktop/tjbot/bootstrap" directory once the setup has completed.

- Use ssh to connect to your device and run the following command to begin the TJBot setup:  
`curl -sL http://ibm.biz/tjbot-bootstrap | sudo sh -`  
This kicks off an install script that will ask several questions.  In most cases, you should just accept the defaults.  However, in a few cases, described below, you may want to deviate from the default:

  - **Hostname**:  
  As previosly mentioned, due to the large number of Raspberry Pis that will be on your network, be sure you have a unique hostname for your device.

  - **Quad 9 Nameservers**:  
  The Quad 9 nameservers (9.9.9.9) are IBM owned and maintained nameservers that focus on security and privacy.  No harm in using them, your choice.

  - **nodejs**:
  Answer **NO** when asked if you would like to install a newer version of Node.js.
  
  - **Install Location**:  
  All of the instructions and scripts for the TJBot recipes expect to find the TJBot code in the default location.  It is definitely best to accept the default here.

  - **LED / Sound Conflict**:  
  On the later models of Raspberry Pi, it no longer appears to be necessary to disable the kernel sound modules in order for the LED to work.  You should answer **No** to this question.  However, the LED and the speaker still have issues working well together.  The resolution of this issue is documented in the [Raspberry Pi 4 LED Bug](#raspberry-pi-4-led-bug) section.

  - **Do not run the Hardware tests when asked**:  
  Due to the LED/Speaker issue as well as a known bug with the Raspberry Pi 4, running the hardware tests at this point is an exercise in futility.  Proper instructions for running the tests later are found in the [Hardware Testing](#hardware-testing) section.

---

## TJBot recipe Notes

You have one of the most most recent versions of the Raspberry Pi (version 4).  However, the TJBot recipes were designed with an older Raspberry Pisin mind.  As a result, there are a couple of places where you will need to apply patches to the code in order for the recipes to work properly.

### Node.js and npm

When executing the `npm install` portion of these recipes, you will likely see the following warnings:

```script
npm WARN npm npm does not support Node.js v10.15.2
npm WARN npm You should probably upgrade to a newer version of node as we
npm WARN npm can't make any promises that npm will work with this version.
npm WARN npm Supported releases of Node.js are the latest release of 4, 6, 7, 8, 9.
npm WARN npm You can find the latest version at https://nodejs.org/
```

You can safely ignore these warnings and everything will work fine.  

**DO NOT UPDATE node.js at this time, it breaks stuff.**  

### Hardware Testing

There are a few programs in the `~/Desktop/tjbot/bootstrap/tests` directory.  These can be very helpful in ensuring that your led and speaker are properly set up.  As you will be making changes to code, you may run into problems where the application does not work properly.  These tests can eliminate the hardware as the source of the problem and save a lot of time.  The instructions for using the tests are found in the [README.md](https://github.com/ibmtjbot/tjbot/tree/master/tests) file located in `~/Desktop/tjbot/tests`.
> Note: The LED test will require patching before use.  See: [Patching Raspberry Pi 4 Bugs](#patching-raspberry-pi-4-bugs).

### Wiring up the LED

Be sure to use the larger RGB LED that came packaged in a pack of 5.

![RGB LEDs](https://github.com/CaskAle/summit-pi-project/raw/master/images/rgb-led.jpg "RGB LEDs")

**Correct wiring of the LED is critical.  If you wire it incorrectly, you will fry it.**  If you look closely or feel along the base of the LED you will find that one side of the base has been flattened.  When wiring, be sure to have this flat spot facing to the right and you should be all good.  

![LED Wiring](https://github.com/CaskAle/summit-pi-project/raw/master/images/wiring.png "LED Wiring")

![Breadboard](https://github.com/CaskAle/summit-pi-project/raw/master/images/breadboard.jpg "Breadboard")

### Patching Raspberry Pi 4 Bugs

In the hardware tests and in all three of the [recipe instructions](https://github.com/ibmtjbot/tjbot/tree/master/recipes) you will come to a spot where you are instructed to run the command: `npm install`.  **Stop at this point** and instead, execute the following eight steps.  This will patch the LED and microphone bugs to allow them to work properly on a Raspberry Pi 4.

1. Be sure that you are in the appropriate directory for the respective recipe or test you are working on:  
`cd ~/Desktop/tjbot/tests` - for the hardware tests.  
`cd ~/Desktop/tjbot/recipes/speech_to_text` - for the Speech to Text recipe.  
`cd ~/Desktop/tjbot/recipes/conversation` - for the Conversation recipe.  
`cd ~/Desktop/tjbot/recipes/sentiment_analysis` - for the Sentiment Analysis recipe.

1. `npm install`
1. ~~`npm install rpi-ws281x-native@'latest`~~ (skip this)
1. `git clone --single-branch --branch raspi4support https://github.com/jimbotel/rpi_ws281x.git`
1. `cp -r rpi_ws281x/* node_modules/rpi-ws281x-native/src/rpi_ws281x`
1. `npm build node_modules/rpi-ws281x-native`
1. Now, you may proceed with the recipe where you left off.

>**Note: Because each recipe installs its own node_modules via the npm install command, you will need to repeat these steps for each of the TJBot recipes as you are working through them**



- Due to the issue described in the [Speaker Not Working](#speaker-not-working) section, below.  You will need to prevent the LED from get initialized in this recipe. To do this, you will need to make a small edit to the code in the recipe's conversation.js file.  

  - Edit the file `./conversation.js`.
  - Search for a block of code that looks like this (at or near line 26):

    ```javascript
    // these are the hardware capabilities that TJ needs for this recipe
    var hardware = ['microphone', 'speaker', 'led', 'servo', 'camera'];
    if (config.hasCamera == false) {
        hardware = ['microphone', 'speaker', 'led', 'servo'];
    }
    ```

  - Remove the two references to the LED that look like this: **`'led',`**.  Be sure to remove the the tick marks, the word led, and the trailing comma as well.  
  - Save the file and exit the editor.

- Now you may proceed with the recipe.

#### Speaker Not Working

Due to a known issue with the Raspberry Pi involving a conflict between the LED and the 3.5mm speaker output, once you initialize the LED, the speaker will no longer produce audible output.  Unfortunately, the only solution to this is to reboot the device. `sudo reboot`.  Once the reboot has finished, the speaker should work properly.  It will continue to work fine until the LED is initialized.
> Note: This problem only exists when using the 3.5mm jack for sound.  If you happen to have a USB or Bluetooth speaker, you can use that without issue.

#### Speaker Volume

If you are having trouble with the volume level of the speaker, you can adjust to from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise the volume to 100%.  Press `esc` when you are done and it will save and exit.  Now you should be able to hear.