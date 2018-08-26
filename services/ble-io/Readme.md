# ble-io bluetooth service


## bada5555-e91f-1337-a49b-8675309fb099

This is the main service that IO Characteristics belong to.  All bytes in the document are represented by arrays of values 0 to 255.

### 2a56 - Digital Characteristic (write, notify)

This characteristic is for reading and writing digital values to pins.

To turn on pin 13:
write `[13, 1]`

To turn off pin 13:
write `[13, 0]`

You may also write in pairs of bytes at once to change multiple pins.
To turn on pin 12, turn off pin 9, and turn on pin 3 all at once:
write `[12, 1, 9, 0, 3, 1]`


Subscribe for notifications to this characteristic for changes in value.
a notify of `[9, 1]` means that pin 9 has been turned on.


### 2a58 - Analog Characteristic (write, notify)

This characteristic is for reading and writing analog values to pins.  Analog values come in at values 0-1023 and must be split across 2 bytes with Least Significant Bits first: LSB(bits 0-7) followed by the Most Significant Bits: MSB(bits 8-15).  However, simple 8-bit analog [PWM](https://en.wikipedia.org/wiki/Pulse-width_modulation) writes do not need an MSB byte sent.

To write the PWM value 227 to pin 3
write `[3, 227]`

To write a 10-bit value such as 613 to pin 11 you must issue three bytes (pin, LSB, MSB)
write `[11, 101, 2]`

You may also write to multiple pins at once, however you will need to always do them in sets of 3 bytes (pin, LSB, MSB).
To set PWM values of pin 3 to 255 and pin 11 at 127
write `[3, 255, 0, 11, 127, 0]`


Subscribe for notifications to this characteristic for changes in analog values. These will always come back as (pin, LSB, MSB)
a notify of `[15, 202, 2]` means that pin 15's analog reading is 714.


### 2a59 - Config Characteristic (read, write)

This characteristic is used to configure the BLE peripheral's pins.

#### Read version
Doing a read on this characteristic should give you back the BLE-IO version for example: `[1, 0, 0]`

#### SET_PIN_MODE  `244`:

The current supported pin modes are:
* INPUT: 0
* OUTPUT: 1
* ANALOG: 2
* PWM: 3
* SERVO: 4

To set pin 3 to SERVO mode:
write `[244, 3, 4]`


#### REPORT_ANALOG `192`:

To turn on analog reporting for pin 14:
write `[192, 14, 1]`

To turn off analog reporting for pin 14:
write `[192, 14, 0]`

#### REPORT_DIGITAL `208`:

To turn on digital reporting for pin 7:
write `[208, 7, 1]`

To turn off digital reporting for pin 7:
write `[208, 7, 0]`


#### SAMPLING_INTERVAL `122`:

To set the sampling interval to 150 milliseconds:
write `[122, 150]`

To set the sampling interval to 715 milliseconds you'll need LSB and MSB bytes:
write `[122, 203, 2]`


#### SERVO_CONFIG `112`:
To set min and maxes on a servo, you'll need to issue both min and max values as LSB and MSB bytes.

To set the min to 0 and max to 180 on pin 3:
write `[112, 3, 0, 0, 180, 0]`

#### I2C_CONFIG `120`:
To configure an i2c bus, you'll need to send a bus number, SDA and SDL pins and delay in microseconds.

For bus 0, pins 20 & 21, with a 3 microsecond delay:
write `[120, 0, 20, 21, 3]`


### 2a60 - I2C Read Characteristic (write, notify)

This characteristic is used to make requests for data as well as subscribe to notifications for those data requests.

You write a request for a single notification, continuous notifications, or to stop continuous notifications. The second byte in the write for these modes are `1`, `2`, or `3` respectively.

* For a single notification on i2c bus 0 at address 27 and register 4 for 15 bytes write: `[0, 1, 27, 4, 15]`
* For a continuous notifications on i2c bus 0 at address 27 and register 4 for 15 bytes write: `[0, 2, 27, 4, 15]`
* To stop notifcations on i2c bus 0 at address 27: `[0, 3, 27]`

i2c read Notifications arrive in the format: `[0, 27, 4, 75, x, x, x...]` where the first byte is the bus number, second byte is the address, the third byte is the register, fourth byte is the number of message bytes, and all subsequent bytes are the i2c message data.


### 2a61 - I2C Write Characteristic (write)

This characteristic is used to write data to an i2c bus, address, and register.

For example, to write bytes on i2c bus 0, at address 110, register 0, and 67 bytes of data write: `[0, 110, 0, 67, x, x, x...]` where the first byte is the bus number, second byte is the address, the third byte is the register, fourth byte is number of message bytes, and all subsequent bytes are the i2c message data.


## Peripheral Implementations
  * [Intel Curie](https://github.com/monteslu/ble-io/tree/master/arduino/curie) Arduino 101 & TinyTile boards
  * [ESP32](https://github.com/monteslu/ble-io/tree/master/arduino/esp32) Lolin32, Lolin32 Lite, and other boards.

## Client Implementations
  * [ble-io](https://github.com/monteslu/ble-io) JavaScript (Node.js and Browser)
