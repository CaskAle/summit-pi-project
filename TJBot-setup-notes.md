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
  You should answer **No** to this question.  Keep in mind that you will need to run the LED based recipes and the speaker based recipes separately.  The LED and the 3.5mm speaker jack do not work well together.  The resolution of this issue is documented in the [Garbled Sound](#garbled-sound) section.

  **Hardware Tests:**  
  **Optional but recommended**.   If you choose not to run them now, there are instructions for running them later in the [Perform Hardware Tests](#perform-hardware-tests) section.

## Update your Raspberry Pi OS and Node.js

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

## Do the Hardware Tests

There are a few test programs in the `~/Desktop/tjbot/tests` directory.  These can be very helpful in ensuring that your led and speaker are properly set up.  As you will be making changes to code, you may run into problems where the application does not work properly.  These tests can eliminate the hardware as the source of the problem and save a lot of time.  The instructions for using the tests are found in the [README.md](https://github.com/ibmtjbot/tjbot/tree/master/tests) file located in `~/Desktop/tjbot/tests`.

## Proper LED Wiring

Be sure to use the larger "NeoPixel" RGB LED that came packaged in a separate pack of 5.

![RGB LEDs](https://github.com/CaskAle/summit-pi-project/raw/master/images/rgb-led.jpg "RGB LEDs")

>**Note: Correct wiring of the LED is critical.  If you wire it incorrectly, you will burn it out.**  

If you look closely or feel along the base of the LED you will find that one side of the base has been flattened.  When wiring, be sure to have this flat spot facing to the right and you should be all good.  

![LED Wiring](https://github.com/CaskAle/summit-pi-project/raw/master/images/wiring.png "LED Wiring")

![Breadboard](https://github.com/CaskAle/summit-pi-project/raw/master/images/breadboard.jpg "Breadboard")

## Speaker/Microphone Issues

### Garbled Sound

Due to a known issue with the Raspberry Pi involving a conflict between the LED and the 3.5mm speaker output, once you initialize the LED, the speaker will no longer produce audible output.  Unfortunately, the only solution to this is to reboot the device (`sudo reboot`).  Once the reboot has finished, the speaker should work properly.  It will continue to work fine until the next time the LED is initialized.
> **Note:** This problem only exists when using the 3.5mm jack for sound.  If you happen to have a USB or Bluetooth speaker, you can use that without issue.

### Disable LED and Servo in Conversation Recipe

Because we do not provide you a servo in the kit and also to avoid conflicts with the built in Raspberry Pi speaker jack, you will want to avoid initializing the LED and servo devices when working on the conversation recipe.  To do this,  edit the `tjbot/recipes/conversation/conversation.js` file.  Near the top of that file, find the line (~ line 22) that looks like this:

``` javascript
const hardware = [TJBot.HARDWARE.MICROPHONE, TJBot.HARDWARE.SPEAKER, TJBot.HARDWARE.LED_NEOPIXEL, TJBot.HARDWARE.SERVO];
```

Delete the `TJBot.HARDWARE.LED_NEOPIXEL, TJBot.HARDWARE.SERVO` entries from this line so that it ends up looking like this:

``` javascript
const hardware = [TJBot.HARDWARE.MICROPHONE, TJBot.HARDWARE.SPEAKER];
```

>Note: Of course, if you happen to have your own servo or are using a USB speaker there is no need to delete the respective hardware entry `TJBot.HARDWARE.SERVO` or `TJBot.HARDWARE.LED_NEOPIXEL` entry.  Just be sure to not leave any extra commas hanging around.

Save and exit the file editor.  This will keep the LED and servo from being initialized.  If, due to running other recipes, the LED has already been initialized, you will likely need to reboot your Raspberry Pi before the speaker will work properly.

## Speaker and Microphone issues

Recent Raspberry Pi OS updates have changed the order of the sound device initialization.  Previously, the 3.5mm headphone jack was always considered to be card 0.  Now, if an HDMI device is detected (such as a display with a speaker), it will take the card 0 spot and the 3.5mm jack will become card 1.  This, in turn, effects the microphone as well, making it go from card 1 to card 2.  This creates a problem with the TJ Bot recipes as they are written to assuming that the 3.5 jack is card 0 and the microphone is card 1 by default.  

The need to know which sound card devices to use is also an issue if you want to use a USB sound card to prevent conflicts with the LED.  

Once you have determined the correct sound card to use, it is a simple exercise to override the default sound devices that are specified in the recipes/tests.

### Determine the speaker card

1. In a terminal, execute the command: `aplay -l`
1. Look at the output for the apporpriate card.  In the example below, you will see that the "Headphones" are card 1.
1. Make a note of the appropriate card number

``` text
**** List of PLAYBACK Hardware Devices ****
card 0: b1 [bcm2835 HDMI 1], device 0: bcm2835 HDMI 1 [bcm2835 HDMI 1]
  Subdevices: 4/4
  Subdevice #0: subdevice #0
  Subdevice #1: subdevice #1
  Subdevice #2: subdevice #2
  Subdevice #3: subdevice #3
card 1: Headphones [bcm2835 Headphones], device 0: bcm2835 Headphones [bcm2835 Headphones]
  Subdevices: 4/4
  Subdevice #0: subdevice #0
  Subdevice #1: subdevice #1
  Subdevice #2: subdevice #2
  Subdevice #3: subdevice #3
```

### Determine the microphone card

1. In a terminal, execute the command: `arecord -l`
1. Look at the output for the apporpriate card.  In the example below, you will see that the "USB PNP Sound Device" is card 2.
1. Make a note of the appropriate card number

``` text
**** List of CAPTURE Hardware Devices ****
card 2: Device [USB PnP Sound Device], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

### Modify the recipe defaults

In each recipe or test that uses the microphone or speaker, you will need to paste the following code:

``` javascript
tjConfig.listen = {
   microphoneDeviceId: 'plughw:2,0',
   backgroundAudioSuppression: 0.4,
   inactivityTimeout: -1,
   language: TJBot.LANGUAGES.LISTEN.ENGLISH_US
};

tjConfig.speak = {
   speakerDeviceId: 'plughw:1,0',
   language: TJBot.LANGUAGES.SPEAK.ENGLISH_US,
   voice: undefined
};
```

just above the line that reads:  
for the test scipts:

``` javascript
tjbot.initialize...
```

for the recipes:

``` javascript
//instantiate our TJBot!
```

In each case, you should replace the first number in the `'plughw:1,0'` with the appropriate sound card number. 
- Use the microphone card number (`arecord -l`) for the `tjConfig.Listen`
- Use the speaker card number (`aplay -l`) for the `tjConfig.Speak`.  

You should then be able to proceed with the tests/recipes as normal.

### Speaker Volume

If you are having trouble with the volume level of the speaker, you can adjust it from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise the volume to 100%.  Press `esc` when you are done and it will save and exit.  Now you should be able to hear the output at a much better volume.
