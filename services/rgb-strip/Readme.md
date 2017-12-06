# RGB Strip
A generic service for controlling a RGB or RGB+W strip of LEDs such as neopixels.

## bada5566-e91f-1337-a49b-8675309fb069

## Characteristics

### e92f  write RGB pixel value(s)

* one pixel: [pin #, pixel #, R,G,B]
  * example: [6, 2, 127,0,200]

* multiple pixels: [pin #, pixel #, R,G,B, pixel #, R,G,B]
  * example: [6, 2, 127,0,200, 3, 55,210,15]

### e93f  write RGBW pixel value(s)

* one pixel: [pin #, pixel #, R,G,B,W]
  * example: [6, 2, 127,0,200,255]

* multiple pixels: [pin #, pixel #, R,G,BW, pixel #, R,G,BW]
  * example: [6, 2, 127,0,200,255, 3, 55,210,15,127]

### e94f  set entire strip color
* set RGB value: [pin #, R,G,B]
  * example: [6, 127,0,200]

* set RGBW value: [pin #, R,G,B,W]
  * example: [6, 127,0,200,127]

### e94f  shift pixels
* shift pixels [direction(0=forward, 1=backward), # spaces]
  * example (shift strip 2 spots forward): [0, 2]
  * example (shift 5 spots backward): [1, 5]

### e95f  configure strip
* set number of pixels on a pin: [pin#, # of pixels]
  * example: [6, 15]

* for a strip larger than 255 pixels use bytes in LSB,MSB format.
  * example (613 pixels on pin 7): [7, 101, 2]


## Peripheral Implementations
  * soon

## Client Implementations
  * any BLE client
