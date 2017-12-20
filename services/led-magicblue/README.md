# 💡 led-magicblue

A service for controlling a Bluetooth LE based lightbulb sold under the brandname MagicBlue.

To identify a lightbulb that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | LEDBLE                                           |
| Service        | `FFF0` or `0000FFF0-0000-1000-8000-00805F9B34FB` | 


## Turn on or off

When the device is off, it will still accept commands, but the lightbulb does not emit any light. Turning the lightbulb on, will restore the same color or preset from before it was turned off.

It is possible to turn the lightbulb on or off by sending a  message to the `FFE9` characteristic of the `FFE5` service. 

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 0        | `CC`          |                              |
| 1        | `23` or `24`  | On is `23`, off is `24`      |
| 2        | `33`          |                              |

For example:
- Turn the lightbulb off:
  `CC 24 33`
- Turn the lightbulb on:
  `CC 23 33`


## Set the lightbulb to a color

It is possible to change the color of the lightbulb by sending a  message to the `FFE9` characteristic of the `FFE5` service. 

The message is 7 bytes and contains the color intensities for red, green and blue and a number of other fixed bytes. 

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 0        | `56`          |                              |
| 1        | `00` - `FF`   | Red color intensity          |
| 2        | `00` - `FF`   | Green color intensity        |
| 3        | `00` - `FF`   | Blue color intensity         |
| 4        | `00`          |                              |
| 5        | `F0`          |                              |
| 6        | `AA`          |                              |

For example:
- Changing the color to yellow:
  `56 FF FF 00 00 F0 AA`
- Changing the color to blue: 
  `56 00 00 FF 00 F0 AA`


## Set the lightbulb to white

It is possible to change the color of the lightbulb to plain white by sending a message to the `FFE9` characteristic of the `FFE5` service. 

The message is again 7 bytes and contains the intensities for white and a number of other fixed bytes. 

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 0        | `56`          |                              |
| 4        | `00`          |                              |
| 4        | `00`          |                              |
| 4        | `00`          |                              |
| 3        | `00` - `FF`   | White color intensity        |
| 5        | `0F`          |                              |
| 6        | `AA`          |                              |

For example:
- Changing the color to 10% white:
  `56 00 00 00 0A 0F AA`
- Changing the color to 100% white: 
  `56 00 00 00 FF 0F AA`


### Selecting a preset

Some versions of these lightbulbs have a number of preset animation which can be enabled by sending a simple message to the `FFE9` characteristic of the `FFE5` service. 

The message is 4 bytes and contains one byte for selecting an animation and one for setting the speed of the animation

| Position | Value         | Details                           |
| -------- | ------------- | --------------------------------- |
| 0        | `BB`          |                                   |
| 1        | `25` - `38`   | Animation preset: see table below |
| 2        | `00` - `FF`   | Speed of animation                |
| 3        | `44`          |                                   |

Presets for animation:

| Value | Details                    |
| ----- | -------------------------- |
| `25`  | Seven color cross fade     |
| `26`  | Red gradual change         |
| `27`  | Green gradual change       |
| `28`  | Blue gradual change        |
| `29`  | Yellow gradual change      |
| `2A`  | Cyan gradual change        |
| `2B`  | Purple gradual change      |
| `2C`  | White gradual change       |
| `2D`  | Red-Green cross fade       |
| `2E`  | Red-Blue cross fade        |
| `2F`  | Green-Blue cross fade      |
| `30`  | Seven color strobe flash   |
| `31`  | Red strobe flash           |
| `32`  | Green strobe flash         |
| `33`  | Blue strobe flash          |
| `34`  | Yellow strobe flash        |
| `35`  | Cyan strobe flash          |
| `36`  | Purple strobe flash        |
| `37`  | White strobe flash         |
| `38`  | Seven color jumping change |

For example:
- Setting a purple strobe flash:
  `44 36 10 44`


## Reading the current state

Unfortunatly it is not possible to read the value of the lightbulb directly. Instead we write the message `EF 01 77` to the `FFE9` characteristic of the `FFE5` service to request a notification with the current value.

We then listen for notifications on the `FFE4` characteristic of the `FFE0` service. 

The data we receive back has the following format:

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 1        |               | Device type                  |
| 2        | `23` - `24`   | On is `23`, off is `24`      |
| 3        | `25` - `38`   | Animation preset             |
| 4        |               |                              |
| 5        | `00` - `FF`   | Speed of animation           |
| 6        | `00` - `FF`   | Red color intensity          |
| 7        | `00` - `FF`   | Green color intensity        |
| 8        | `00` - `FF`   | Blue color intensity         |
| 9        | `00` - `FF`   | White color intensity        |
| 10       |               | Version                      |
