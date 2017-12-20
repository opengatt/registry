# 🌡 sensor-nrf51tag

A service for reading the values of nRF51 based sensor tag. There seems to be a couple of variants of this tag available on sites like AliExpress.

To identify a sensor tag that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | SENSOR
| Service        | `6E400001-B5A3-F393-E0A9-E50E24DCCA9E` | 

Important note: the service UUID is identical to the Nordic UART service, but this is NOT because it is a protocol on top of the UART service. The firmware of this device is probably based on sample code provided by Nordic and the result is a genuine UUID numbering conflict.

All data can be read by registering for notification on the various characteristics. Whenever the value is changed, you will get a notification with the current value.


## Acceleration

In order to receive this data, you will need to listen for notifications on the `6E400002-B5A3-F393-E0A9-E50E24DCCA9E` characteristic.

The data received consists of three 16 bit signed integers (big endian) which contain the raw accelerometer data for each of the three axis. 

The range of each axis is from -32.768 to 32.767, which corresponds to -2 G to 2 G. So to get the actual g-force for each axis, you need to multiply by 2 and divide by 32.768.

For example: a sensor that is lying perfectly flat and still may have the following raw data: `FF B8 FE 2C 39 18`. 

- The X axis is `FF B8` would mean -72 * 2 / 32.768 = -0,004 G
- The Y axis is `FE 2C` would mean -468 * 2 / 32.768 = -0,028 G
- The Z axis is `39 18` would mean 14616 * 2 / 32.768 = 0,892 G

This is aproximately what we expect, with the X and Y axis being 0 G and the Z axis around 1 G.


## Gyroscope

In order to receive this data, you will need to listen for notifications on the `6E400003-B5A3-F393-E0A9-E50E24DCCA9E` characteristic.

The data received consists of three 16 bit signed integers (big endian) which contain the raw gyroscope data for each of the three axis. 

The range of each axis is from -32.768 to 32.767, which corresponds to -250 °/sec to 250 °/sec. 

For example: a sensor that is lying perfectly still may have the following raw data: `FF 34 FF BC FF E8`. 

- The X axis is `FF 34` would mean -204 * 250 / 32.768 = -1,5 °/sec
- The Y axis is `FF BC` would mean -68 * 250 / 32.768 = -0,5 °/sec
- The Z axis is `FF E8` would mean -24 * 250 / 32.768 = -0,1 °/sec

Which is was we expect from a stationary sensor. All values should be near 0. When we move the sensor, the values all change immediately and as soon as the sensor is stationary again, the values change back to 0.


## Atmospheric pressure

In order to receive this data, you will need to listen for notifications on the `6E400004-B5A3-F393-E0A9-E50E24DCCA9E` characteristic.

The data received consists of a 32 bit integer (big endian), which contains the current atmospheric pressure in Pascals.

For example:
- the value `00 01 93 27` would mean 103207 Pa or 1,03207 bar.


## Temperature

In order to receive this data, you will need to listen for notifications on the `6E400005-B5A3-F393-E0A9-E50E24DCCA9E` characteristic.

The data received consists of a 16 bit integer (big endian), which contain the current temperature in degrees Celcius, multiplied by 10. To get the proper temperature you would need to divide this value by 10.

For example:
- the value `00 FA` would mean 250 / 10 = 25,0 °C
- the value `01 08` would mean 264 / 10 = 26,4 °C


## Ambient light

In order to receive this data, you will need to listen for notifications on the `6E400006-B5A3-F393-E0A9-E50E24DCCA9E` characteristic.

The data received consists of a 16 bit integer (big endian) and can be converted to lux by dividing the value by 0,36.

For example:
- the value `00 0D` would mean 13 / 0,36 = 36 lux.
- the value `59 59` would mean 22937 / 0,36 = 63.713 lux.
