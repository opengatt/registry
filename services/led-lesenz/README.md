# 💡 led-lesenz

A service for controlling a Bluetooth LE based lightbulb sold under the brandname LeSenz.

To identify a lightbulb that supports this protocol, use the following name prefix or advertised service:

|                |                                                  |
| -------------- | ------------------------------------------------ |
| Name           | B730                                             |
| Service        | `FFF0` or `0000FFF0-0000-1000-8000-00805F9B34FB` | 


## Set the lightbulb to a color

It is possible to change the color of the lightbulb by sending a message to the `FFF1` characteristic of the `FFF0` service. 

| Position | Value         | Details                      |
| -------- | ------------- | ---------------------------- |
| 0        | `AA`          |                              |
| 1        | `0A`          |                              |
| 2        | `FC`          |                              |
| 3        | `3A`          |                              |
| 4        | `86`          |                              |
| 5        | `01`          |                              |
| 6        | `0D`          |                              |
| 7        | `06`          |                              |
| 8        | `01`          |                              |
| 9        | `00` - `FF`   | Red color intensity          |
| 10       | `00` - `FF`   | Green color intensity        |
| 11       | `00` - `FF`   | Blue color intensity         |
| 12       | `00`          |                              |
| 13       | `00`          |                              |
| 14       | `00`          |                              |
| 14       | `00` - `FF`   | Random number to ensure unique consequitive messages |
| 15       | `00` - `FF`   | Checksum, see below          |
| 16       | `0D`          |                              |

### Calculating the checksum

We start out with the value of `55` and then iterate over the message from position 1 to 14 and add all the bytes to our checksum. 

```
checksum = 0x55;

for (i = 1; i <= 14; i++) {
    checksum += message[i];
}

message[15] = checksum & 0xff;
```
