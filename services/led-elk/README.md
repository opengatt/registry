# 💡 led-elk

A service for controlling a Bluetooth LE based LED board, possibly created by Elink Co.. There seems to be a couple of variants of this board available on sites like AliExpress.

To identify a LED board that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | ELK-BLEDW
| Service        | `FFF0` or `0000FFF0-0000-1000-8000-00805F9B34FB` | 


## Sending commands

It is possible to send commands to the LED board by writing a message to the `FFF3` characteristic of the `FFF0` service. 

Each message needs to be 9 bytes longs and starts with one byte with the value `7E` and ends with a byte with the value `EF`. The values of the bytes between these first and last byte differ between commands.


### Turn on or off

Turn the device on or off. When the device is off, it will still accept commands, but the LEDs do not emit any light. Turning the device on, will restore the same color or preset from before it was turned off.

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `04 04`       | Turn on or off                      |
| Parameter     | i             | On = `01`, Off = `00`               |
| Fixed         | `FF FF FF 00` |                                     |
| End           | `EF`          |                                     |

For example:
- Turn the device off:
  `7E 04 04 00 FF FF FF 00 EF`
- Turn the device on:
  `7E 04 04 01 FF FF FF 00 EF`


### Set light to a color

Change the color of the device. Each of the 8 LEDs of the device can be changed collectively to an RGB color. 

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `07 05 03`    | Change color                        |
| Parameter     | r             | Red color component: `00` - `FF`    |
| Parameter     | g             | Green color component: `00` - `FF`  |
| Parameter     | b             | Blue color component: `00` - `FF`   |
| Fixed         | `00`          |                                     |
| End           | `EF`          |                                     |

For example:
- Changing the color to yellow:
  `7E 07 05 03 FF FF 00 00 EF`
- Changing the color to blue: 
  `7E 07 05 03 00 00 FF 00 EF`


### Set light to neutral white

Change the color of the device to a specific intensity of plain white. 

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `05 05 01`    | Change color                        |
| Parameter     | i             | Intensity of white: `00` - `64`     |
| Fixed         | `FF FF 08`    |                                     |
| End           | `EF`          |                                     |

For example:
- Change to 30% white:
  `7E 05 05 01 1E FF FF 08 EF`
- Change to 90% white:
  `7E 05 05 01 5A FF FF 08 EF`


### Set light to warm or cool white

Change the color of the device to a specific intensity of plain white. 

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `06 05 02`    | Change color                        |
| Parameter     | w             | Intensity of warm: `00` - `64`      |
| Parameter     | c             | Intensity of cool: `00` - `64`      |
| Fixed         | `FF 08`       |                                     |
| End           | `EF`          |                                     |

The intensity of cool should be `64` - the intensity of warm. So when warm is `64`, cool should be `00` and when warm is `00`, cool should be `64`.

For example:
- Change to 10% warm and 90% cool:
  `7E 06 05 02 0A 5A FF 08 EF`


### Selecting a preset

This device has about 30 different presets or animations that can be selected. The presets range from just a static color, to an animation using different colors.

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `05 03`       | Select preset                       |
| Parameter     | value         | Preset value: see tables below      |
| Parameter     | type          | Preset type: see tables below       |
| Fixed         | `FF FF 00`    |                                     |
| End           | `EF`          |                                     |

Presets for neutral white light:

| Type | Value | Details              |
| ---- | ----- | -------------------- |
| `01` | `80`  | White light 0%       |
| `01` | `81`  | White light 10%      |
| `01` | `82`  | White light 20%      |
| `01` | `83`  | White light 30%      |
| `01` | `84`  | White light 40%      |
| `01` | `85`  | White light 50%      |
| `01` | `86`  | White light 60%      |
| `01` | `87`  | White light 70%      |
| `01` | `88`  | White light 80%      |
| `01` | `89`  | White light 90%      |
| `01` | `8A`  | White light 100%     |


Presets for warm or cool white light:

| Type | Value | Details              |
| ---- | ----- | -------------------- |
| `02` | `80`  | Warm 0%, Cool 100%   |
| `02` | `81`  | Warm 10%, Cool 90%   |
| `02` | `82`  | Warm 20%, Cool 80%   |
| `02` | `83`  | Warm 30%, Cool 70%   |
| `02` | `84`  | Warm 40%, Cool 60%   |
| `02` | `85`  | Warm 50%, Cool 50%   |
| `02` | `86`  | Warm 60%, Cool 40%   |
| `02` | `87`  | Warm 70%, Cool 30%   |
| `02` | `88`  | Warm 80%, Cool 20%   |
| `02` | `89`  | Warm 90%, Cool 10%   |
| `02` | `8A`  | Warm 100%, Cool 0%   |


Presets for color and animation:

| Type | Value | Details              |
| ---- | ----- | -------------------- |
| `03` | `80`  | Static red           |
| `03` | `81`  | Static green         |
| `03` | `82`  | Static blue          |
| `03` | `83`  | Static white         |
| `03` | `84`  | Static yellow        |
| `03` | `85`  | Static cyan          |
| `03` | `86`  | Static purple        |
| `03` | `87`  | Tricolor jump        |
| `03` | `88`  | Seven-color jump     |
| `03` | `89`  | Tricolor gradient    |
| `03` | `8A`  | Seven-color gradient |
| `03` | `8B`  | Red gradient         |
| `03` | `8C`  | Green gradient       |
| `03` | `8D`  | Blue gradient        |
| `03` | `8E`  | Yellow gradient      |
| `03` | `8F`  | Cyan gradient        |
| `03` | `90`  | Purple gradient      |
| `03` | `91`  | White gradient       |
| `03` | `92`  | Red-Green gradient   |
| `03` | `93`  | Red-Blue gradient    |
| `03` | `94`  | Green-Blue gradien   |
| `03` | `95`  | Seven-color flash    |
| `03` | `96`  | Red flash            |
| `03` | `97`  | Green flash          |
| `03` | `98`  | Blue flash           |
| `03` | `99`  | Yellow flash         |
| `03` | `9A`  | Cyan flash           |
| `03` | `9B`  | Purple flash         |
| `03` | `9C`  | White flash          |

For example:
- Select the 50% white light preset:
  `7E 05 03 85 01 FF FF 00 EF`
- Select the warm 80%, cool 20% preset:
  `7E 05 03 88 02 FF FF 00 EF`
- Select the static yellow preset: 
  `7E 05 03 84 03 FF FF 00 EF`
- Select the seven-color flash preset:
  `7E 05 03 95 03 FF FF 00 EF`


### Changing the brightness

Change the brightness of the LED's. This affects both solid colors as presets.

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `04 01`       | Change brightness                   |
| Parameter     | i             | Brightness: `00` - `64`             |
| Fixed         | `FF FF FF 00` |                                     |
| End           | `EF`          |                                     |

For example:
- Set the brightness to 10%
  `7E 04 01 0A FF FF FF 00 EF`
- Set the brightness to 100%
  `7E 04 01 64 FF FF FF 00 EF`


### Changing the speed

Change the speed of an animation that is running. This affects only presets that are not a static color.

| Type          | Value         | Details                             |
| ------------- | ------------- | ----------------------------------- |
| Start         | `7E`          |                                     |
| Command       | `04 02`       | Change speed                        |
| Parameter     | i             | Speed: `00` - `64`                  |
| Fixed         | `FF FF FF 00` |                                     |
| End           | `EF`          |                                     |

For example:
- Set the speed to 10%
  `7E 04 02 0A FF FF FF 00 EF`
- Set the speed to 100%
  `7E 04 02 64 FF FF FF 00 EF`
