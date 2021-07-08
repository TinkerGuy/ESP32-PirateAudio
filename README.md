# ESP32-PirateAudio
## Why this board was developed:

I built a player with Raspberry Pi Zero and Pirate Audio Amp HAT ([Link](https://shop.pimoroni.com/products/pirate-audio-3w-stereo-amp "Link")) some time ago.
This compact electronics found a place in a 3D-printed case.
Then, when a Squeezelite firmware for Esspressif's ESP32 microcontroller was developed and released by Phillipe, the idea came to me to convert my player to the ESP32. Since the space in the case is tailor-made for the Raspberry Pi Zero,
a board in exactly this format was needed. The Pirate Audio Board was developed especially for the Raspberry Pi, so my ESP32 board had to get the 40 pin Raspberry Pi header as well. On the Pirate board are two MAX9857 I2S amps (2x 3W), a SPI display with ST7789 controller and four buttons.
Which connections are necessary to be able to use all functions further, I took from this page ([Link](https://de.pinout.xyz/pinout/pirate_audio_3w_amp# "Link")).
Unfortunately there is an error, which took me some time to find. The display is not powered by the 5V supply via a voltage regulator,
but it gets its 3.3V voltage directly from the Raspberry via pin17. And only via pin17 and not via pin1. Although on the Raspberry Pi Pin1 and Pin17 carry 3,3V,
on the Pirate board only Pin17 is connected.
My first board design did not have this connection. I did connect 3.3V to Pin1, but I did not connect it to Pin17.
Therefore I had to solder this bridge afterwards. In the meantime I changed the PCB design, now there is 3,3V at Pin1 as well as at Pin17.
Now the Pirate board works one hundred percent on my ESP32 board.

## What can be done with it:

With this board in combination with Phillipe's Squeezelite ESP32 firmware and the Pirate Audio Amp HAT, you can build a very compact player for the Logitech Media Server (LMS). All that is needed is a 5V/2A power supply and a pair of speakers.
Since the Pirate Audio Board uses the standard I2S interface of the Raspberry Pi, it should also be possible to use a Hifiberry Miniamp if you don't need a display and buttons.
You can't use a "big" Hifiberry Amp without modifications, because it also needs the I2C interface (Pin3 and Pin5). These are not wired on my board.

## Make a PCB and solder SMD components:

With the production files provided here (Gerber Files) as well as the files BOM.csv (component list) and Pick&Place.csv (component positions) you can have a PCB service provider
(e.g. [Link](https://jlcpcb.com "Link")).
The Gerber files have to be sent as .zip file to the service provider. These contain all data to be able to manufacture the PCB. Most of the providers I know have a minimum order quantity of 5 pieces. It is recommended to have the SMD components soldered on by the service provider. For this the two .csv files are necessary.
The BOM.csv contains the component list. When ordering, make sure that the components are replaced by standard parts from the service provider. The electrical values must be kept. The ESP32 WROVER module can be used in the 8MB PSRAM as well as in the 16MB PSRAM version. Also the version simply named ESP32-WROVER-B works.
All modules labeled WROOM will not work because they do not have external PSRAM installed.
Note: The through-hole components (pin header, 5V power jack, etc.) are better soldered by yourself, if you have the possibility. These parts are also soldered by hand by the service provider, which is a bit more expensive. This task can be done quite well by yourself, provided you have some knowledge in soldering.

## Board layout:

The following table shows the connections between ESP32 and the 40 Pin Header:

| ESP32-WROVER-B | 40 Pin Header |
| :------------: | :------------: | :------------: |
| 5V | NC | Pin 2 |
| 3,3V | Pin 2 (VCC) | Pin 1, 17 |
| GND | Pin 1, 15, 38 | Pin 6, 9, 14, 20, 25, 30, 34, 39 |
|<span style="color:green"> I2S (Clock)</span> | <span style="color:green">Pin 16 (GPIO 13)</span> | <span style="color:green">Pin 12</span> |
| <span style="color:green">I2S (Data)</span> | <span style="color:green">Pin 11(GPIO 26)</span> | <span style="color:green">Pin 40</span> |
| <span style="color:green">I2S (WordSelect)</span> | <span style="color:green">Pin 12 (GPIO 27)</span> | <span style="color:green">Pin 35</span> |
| <span style="color:green">Amp Enable</span> | <span style="color:green">Pin 13 (GPIO 14)</span> | <span style="color:green">Pin 22</span> |
| <span style="color:red">SPI (Clock)</span> | <span style="color:red">Pin 16 (GPIO 13)</span> | <span style="color:red">Pin 23</span> |
| <span style="color:red">SPI (Data)</span> | <span style="color:red">Pin 36 (GPIO 22)</span> | <span style="color:red">Pin 19</span> |
| <span style="color:red">SPI (DC)</span> | <span style="color:red">Pin 33 (GPIO 21)</span> | <span style="color:red">Pin 21</span> |
| <span style="color:red">SPI (CS)</span> | <span style="color:red">Pin 9 (GPIO 33)</span> | <span style="color:red">Pin 24</span> |
| <span style="color:red">Backlight</span> | <span style="color:red">Pin 8 (GPIO 32)</span> | <span style="color:red">Pin 33</span> |
| <span style="color:blue">Button A</span> | <span style="color:blue">Pin 24 (GPIO 2)</span> | <span style="color:blue">Pin 29</span> |
| <span style="color:blue">Button B</span> | <span style="color:blue">Pin 26 (GPIO 4)</span> | <span style="color:blue">Pin 31</span> |
| <span style="color:blue">Button X</span> | <span style="color:blue">Pin 29 (GPIO 5)</span> | <span style="color:blue">Pin 36</span> |
| <span style="color:blue">Button Y (old)</span> | <span style="color:blue">Pin 30 (GPIO 18)</span> | <span style="color:blue">Pin 18</span> |
| <span style="color:blue">Button Y (new)</span> |<span style="color:blue">Pin 23 (GPIO 15)</span> | <span style="color:blue">Pin 38</span> |
| 10 kOhm Pullup | Pin EN | NC |
| Jumper to GND | Pin 25 (GPIO 0) | NC |

<span style="color:green">Sound (DAC)</span>&emsp;<span style="color:red">Display</span>&emsp;<span style="color:blue">Buttons</span>.

## Flash the firmware and put it into operation:

Before you can configure the board and put it into operation, the firmware must be flashed once by Phillipe. Further updates can be done OTA via the web interface.
The current firmware (at the moment this one: squeezelite-esp32-master-cmake-I2S-4MFlash-16-1.698.zip) as well as a manual for flashing can be found here: (link)
My ESP32 board does not have a USB-to-serial chip, so an external module is needed for flashing. I can recommend this module: (image).
To put the ESP32 module into programming mode, the jumper must be closed.

Attention: The 5V power supply must not be connected for programming. The USB-to-Serial module must be set to 3.3V! A too high voltage will destroy the ESP32 module !

The cabling of the serial interface and the setting of the ESP32 flash tool can be found in the Flash directory.
When the firmware has been flashed successfully, the flash tool is closed, the cabling of the serial interface is disconnected and the jumper is opened again.

Then the Pirate board is plugged in and the 5V supply voltage is connected.

## Configuration:

How to connect the board to the wLAN can also be found on Phillipe's or sle118's GitHub page ([Link](https://github.com/sle118/squeezelite-esp32 "Link")).
For the board to be fully functional, the following settings need to be made:

dac_config: `model=I2S,bck=13,ws=27,do=26`

spi_config: `data=21,clk=23,dc=22,host=1`

display_config: `SPI,width=240,height=240,cs=33,back=32,speed=32000000,driver=ST7789`

set_GPIO: `14=amp`

actrls_config: `button`

button: `[{"gpio":4, "type": "BUTTON_LOW", "pull":true, "normal":{"pressed": "ACTRLS_VOLDOWN"}}, {"gpio":2, "type": "BUTTON_LOW", "pull":true, "normal":{"pressed": "ACTRLS_VOLUP"}},{"gpio":5, "type": "BUTTON_LOW", "pull":true, "long_press": 500, "normal":{"pressed": "ACTRLS_PLAY"}, "longpress":{"pressed": "BCTRLS_RIGHT"}},{"gpio":18, "type": "BUTTON_LOW", "pull":true, "long_press":500, "normal":{"pressed": "BCTRLS_DOWN"}, "longpress":{"pressed": "BCTRLS_LEFT"}}]`

Note: On newer Pirate boards button Y is on a different GPIO, here "gpio":18 has to be replaced by "gpio":15.

In the LMS settings you have to install the ESP32 plugin for the display function and configure it according to your preferences. Again, I recommend the instructions from Phillipe.

## Disclaimer:

I recommend the implementation of this project only if appropriate prior knowledge is available.
Improper use can lead to damage. I assume no liability for any damage to people and technology.
The board is designed for a supply voltage of 5V. Wrongly dimensioned power supplies can destroy the electronics.
The ESP32 module may only be supplied with 3.3V voltage.
The supply voltage of 5V is regulated down to 3.3V by a voltage regulator. When connecting the USB-to-serial module, make sure that max. 3.3V voltage is applied to the ESP32 module. In this case the voltage regulator is out of function.
I am not responsible for the content of the linked pages above.
