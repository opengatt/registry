# daydream-controller bluetooth service


## fe55

This is the main service.

### 0000000110001000800000805f9b34fb Controller data stream

Subscribing to this gives a stream of notifications representing controller database
Encoded in these bytes are controller button states as well as accelerometer and gyroscope data.

More info can be found [here](https://github.com/charliegerard/daydream-node/blob/master/daydream-node.js#L48)

### 0000000310001000800000805f9b34fb ?

uknown read characteristic

Reading this gives back 2 bytes such as: `[246,15]`

TODO: figure out what the bytes mean


### 0000000210001000800000805f9b34fb ?

unknown write characteristic


## Client Implementations
  * [daydream-node](https://github.com/charliegerard/daydream-node) JavaScript (Node.js and Browser)
