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
  You should answer **No** to this question.  The LED and the built-in speaker port do not work well together.  The resolution to this issue is documented in the [Garbled Sound](#garbled-sound) section.

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

Be sure to use the larger "NeoPixel" LED that came packaged in a separate pack of five.  For reference, you will see it in the image below as compared to the smaller RGB LED in the kit.

![RGB LEDs](https://github.com/CaskAle/summit-pi-project/raw/master/images/rgb-led.jpg "RGB LEDs")

### Raspberry Pi to LED Pin mapping:

| LED Pin | Pin type | Raspberry Pi Pin | Pin Label |
|---|----------|--------|---------|
| 1 | Data In  | 12     | GPIO 18 |
| 2 | Power    | 1      | +3.3v   |
| 3 | Ground   | 6      | GND     |
| 4 | Data Out | unused |         |

>**Note:** The Neopixel LED has a flat notch on one side.  Use this to properly orient the LED and figure out which pin is which.  If you look closely or feel along the plastic base of the LED you will find that one edge of the round base has been shaved flat.  **The flat edge of the LED is at pin 4 of the LED**.  
Correct wiring of the LED is critical.  If you wire it incorrectly, you will destroy it.

Your kit includes a card (below) that maps out the pins on the Raspberry Pi.  There are 40 pins and they each perform unique function.  The pins have physical numbers (1 - 40) as well as a label.  For reference, you will see on the card that GPIO 18 is on physical pin 12.

![Pin Map](https://github.com/CaskAle/summit-pi-project/raw/master/images/pin-map.jpg "Pin Map")

![LED Wiring](https://github.com/CaskAle/summit-pi-project/raw/master/images/wiring.png "LED Wiring")

As you can see from the above image, it can be rather tedious to find the correct pin on the Raspberry Pi.  That's where the CanaKit GPIO to Breadboard Interface Board (40 Pin T-Shaped) device comes in to play.  This handy device maps the 40 pins of the Raspberry Pi to the receiver holes of a testing breadboard.  As an added bonus, the pins will have appropriate labels to help with pin mapping.  Attach it to you Raspberry Pi as shown in the image below.

![Breadboard Installation](https://github.com/CaskAle/summit-pi-project/raw/master/images/breadboard2.jpg "Breadboard Installation")

Once you have the breadboard attached to the Raspberry Pi, the image below demonstrates the wiring for the LED.  If you are not familiar with the use of a breadboard, take a look at the following short [Breadboard Tutorial](https://www.elecrow.com/blog/how-to-use-a-breadboard-for-beginners.html).

![Breadboard Wiring](https://github.com/CaskAle/summit-pi-project/raw/master/images/breadboard.jpg "Breadboard Wiring")

## Disable LED and Servo in Conversation Recipe

Because we do not provide you a servo in the kit and also to avoid conflicts with the built-in speaker port, you need to avoid initializing the LED and servo devices when working on the conversation recipe.  To do this,  edit the `tjbot/recipes/conversation/conversation.js` file.  Near the top of that file, find the line (~ line 22) that looks like this:

``` javascript
const hardware = [TJBot.HARDWARE.MICROPHONE, TJBot.HARDWARE.SPEAKER, TJBot.HARDWARE.LED_NEOPIXEL, TJBot.HARDWARE.SERVO];
```

Delete the `TJBot.HARDWARE.LED_NEOPIXEL, TJBot.HARDWARE.SERVO` entries from this line so that it ends up looking like this:

``` javascript
const hardware = [TJBot.HARDWARE.MICROPHONE, TJBot.HARDWARE.SPEAKER];
```

>Note: Of course, if you happen to have your own servo or are using a USB speaker there is no need to delete the respective hardware entry `TJBot.HARDWARE.SERVO` or `TJBot.HARDWARE.LED_NEOPIXEL` entry.  Just be sure to not leave any extra commas hanging around.

This will keep the LED and servo from being initialized.  If, due to running other recipes, the LED has already been initialized, you will likely need to reboot your Raspberry Pi before the speaker will work properly.## Speaker/Microphone Issues

### Garbled Sound

Due to a known issue with the Raspberry Pi involving a conflict between the LED and the built-in speaker port, once you initialize the LED, the speaker will no longer produce audible output.  Unfortunately, the only solution to this is to reboot the device (`sudo reboot`).  Once the reboot has finished, the speaker should work properly.  It will continue to work fine until the next time the LED is initialized.
> **Note:** This problem only exists when using the built-in speaker port for sound output.  If you use a USB attached speaker, this conflict will not occur.

## Speaker and Microphone issues

There are two primary reasons that you might run into issues with your audio input and output:

1. You are using speaker plugged into a USB port rather than the built-in speaker port.

1. Recent Raspberry Pi OS updates have changed the order of the audio device initialization.  Previously, the built-in speaker port was always initialized as card 0.  Now, other audio devices can take the card 0 spot and the built-in speaker port will be assigned another number.  This, also, effects the microphone device as well, causing it to initialize as something other than card 1.

Both of these issues creates a problem with the TJ Bot recipes as they are written assuming that the built-in speaker port is card 0 and the microphone is card 1 by default.  Fortunately, it is quite easy to override these defaults in the TJ Bot recipes and tests.  

### Finding the speaker card number

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

### Finding the microphone card number

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

``` javascript
tjbot.initialize...
```

for the test scipts and just above the line that reads:

``` javascript
//instantiate our TJBot!
```

for the recipes.

In each case, you should replace the first number in the `'plughw:1,0'` with the appropriate sound card number.

- Use the microphone card number (`arecord -l`) for the `tjConfig.Listen`
- Use the speaker card number (`aplay -l`) for the `tjConfig.Speak`.  

You should then be able to proceed with the tests/recipes as normal.

### Speaker Volume

If you are having trouble hearing the output from the speaker, you can adjust the volume from the command line with the command: `alsamixer`.  Use the arrow keys on your keyboard to raise or lower the volume.  Press `esc` when you are done and it will save your changes.
