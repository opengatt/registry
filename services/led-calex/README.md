# 💡 led-calex

A service for controlling a Bluetooth LE based lightbulb sold under the brandname Calex and Maginon.

To identify a lightbulb that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | iLedBlub                                         |
| Service        | `CC02` or `0000CC02-0000-1000-8000-00805F9B34FB` | 


### Set light to a color

It is possible to change the color of the lightbulb by sending a message to the `EE03` characteristic of the `CC02` service. 

The message is 10 bytes and consists of 5 double bytes. Each double byte controls one single colour LED. One of the double bytes is not used. The first byte controls whether or not the LED is turned on and can be `00` for off and `01` for on. The second byte controls the intensity of the LED and can be any value between `00` and `FF`. 

There does not seem to be any difference between actually turning the LED off and setting the intensity of the color to `00`. So practically the first byte of each color can always be `01` and the LED will automatically turn off when the intensity is `00`.

The order is green, unused, blue, red and white.

| Position | Value         | Color | Details                      |
| -------- | ------------- | ----- | ---------------------------- |
| 0        | `01`          | Green | On                           |
| 1        | `00` - `FF`   | Green | Color intensity              |
| 2        | `01`          | -     |                              |
| 3        | `00` - `FF`   | -     |                              |
| 4        | `01`          | Blue  | On                           |
| 5        | `00` - `FF`   | Blue  | Color intensity              |
| 6        | `01`          | Red   | On                           |
| 7        | `00` - `FF`   | Red   | Color intensity              |
| 8        | `01`          | White | On                           |
| 9        | `00` - `FF`   | White | Color intensity              |

The color LED's and the white LED cannot be turned on at the same time. If an intensity for the white LED is specified at position 9, the lightbulb will always be just white.

Just speculation, but perhaps the unused double bytes are intended for a second white LED with either warmer or cooler light. 

For example:
- Changing the color to yellow:
  `01 FF 01 00 01 00 01 FF 01 00`
- Changing the color to blue: 
  `01 00 01 00 01 FF 01 00 01 00`
- Changing the color to white: 
  `01 00 01 00 01 00 01 00 01 FF`


## Get the current state

There is a way to get the current color of the lightbulb, but at least the Calex lightbulb does not support this. Perhaps other lightbulbs that use the same protocol do support this.

Unfortunatly it is not possible to read the value of the lightbulb directly. Instead we write the command `71 75 69 6E 74 69 63` to the `EE02` characteristic of the `CC02` service to request a notification with the current value.

We then listen for notifications on the `EE01` characteristic of the `CC02` service. The data that we receive is in the same format as the data we send to change the color. To get the red, green, blue and white color intensities, we need to look at position 7, 1, 5 and 9.