# Nordic UART
A generic service for reading and writing streams of bytes

# 6e400001b5a3f393e0a9e50e24dcca9e


## Characteristics

| ID            | Modes       | Notes        |
| ------------- |-------------| -------------|
| 6e400002b5a3f393e0a9e50e24dcca9e    | write | Transmit aribtrary bytes. |
| 6e400003b5a3f393e0a9e50e24dcca9e     | notify      | Receive bytes from peripheral. |

## Peripheral Implementations
  * [BLEStream](https://github.com/firmata/arduino/blob/master/utility/BLEStream.h) Arduino

## Client Implementations
  * [ble-serial](https://github.com/monteslu/ble-serial) JavaScript (Node.js and Browser)
