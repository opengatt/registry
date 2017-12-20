# 💡 led-playbulb

A service for controlling a Bluetooth LE based lightbulbs sold under the brandname Playbulb.

To identify a lightbulb that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | PLAYBULB                                         |
| Service        | `FF0F` or `0000FF0F-0000-1000-8000-00805F9B34FB` | 


Playbulb uses a very simple protocol to set the color of the lightbulb. We can simply write the color value to the `FFFC` characteristic of the `FF0F` service. The value contains 4 bytes, one for each color LED. The order is White, Red, Green, Blue. It is possible to turn on the white LED together with the colored LEDs, so these lightbulbs are effectively RGBW, instead of just RGB or just W.

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 0        | `00` - `FF`   | White color intensity        |
| 1        | `00` - `FF`   | Red color intensity          |
| 2        | `00` - `FF`   | Green color intensity        |
| 3        | `00` - `FF`   | Blue color intensity         |

For example:
- Changing the color to yellow:
  `00 FF FF 00`
- Changing the color to blue: 
  `00 00 00 FF`
- Changing the color to white: 
  `FF 00 00 00`

Reading the current state is just as simple, as the same characteristic we are using to write the data, also supports reading. So we can retrieve the current color, by simply reading the `FFFC` characteristic of the `FF0F` service.