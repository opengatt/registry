# ⌚️ band-hplus

A service for controlling a Bluetooth LE based sportsbands sold under the brandname HPlus.

To identify a sportsband that supports this protocol, use the following name prefix or advertised service:

| Name           | Service                                |
| -------------- | -------------------------------------- |
| HPLUS          | `14701820-620A-3973-7C78-9CFFF0876ABD` | 


## Reading data from the sportsband

When the user is wearing the sportsband, you can listen to the `14702853-620A-3973-7C78-9CFFF0876ABD` of the `14701820-620A-3973-7C78-9CFFF0876ABD` service to get notified of any changes.

The data we receive back can be of various size depending of the type of message. Different models of the sportsband may use different message types to send the information.

The type is determined by the first byte of the message and can be:

| Type         | Size          | Details                              |
| ------------ | ------------- | ------------------------------------ |
| `33`         | 15 bytes      | Current heartbeat, Steps, etc.       |
| `36`         | 19 bytes      | Summary of daily activity            |
| `39` or `40` | 8 bytes       | 10 minute summary                    |
| `52`         | 19 bytes      | Current heartbeat, Steps, etc.       |



The message of type `33` has the following format:

| Position | Value             | Details                      |
| -------- | ----------------- | ---------------------------- |
| 0        | `33`              |                              |
| 1 - 2    | `00 00` - `FF FF` | Step count                   |
| 3 - 4    | `00 00` - `FF FF` | Distance                     |
| 5 - 6    | `00 00` - `FF FF` | Calories burnt (active)      |
| 7 - 8    | `00 00` - `FF FF` | Calories burnt (resting)     |
| 9        | `00` - `FF`       | Battery                      |
| 10       | `00` - `FF`       | Temperature                  |
| 11       | `00` - `FF`       | Heartrate (bpm)              |
| 12       | `00`              |                              |
| 13       | `00`              |                              |
| 14       | `00`              |                              |


The message of type `36` has the following format:

| Position | Value             | Details                      |
| -------- | ----------------- | ---------------------------- |
| 0        | `36`              |                              |
| 1 - 2    | `00 00` - `FF FF` | Step count                   |
| 3 - 4    | `00 00` - `FF FF` | Distance                     |
| 5 - 6    | `00 00` - `FF FF` | Calories burnt (active)      |
| 7 - 8    | `00 00` - `FF FF` | Calories burnt (resting)     |
| 9 - 10   | `00 00` - `FF FF` | Year                         |
| 11       | `00` - `FF`       | Month                        |
| 12       | `00` - `FF`       | Day                          |
| 13       | `00`              |                              |
| 14       | `00`              |                              |
| 15       | `00` - `FF`       | Maximum Heartrate (bpm)      |
| 16       | `00` - `FF`       | Minimum Heartrate (bpm)      |
| 17       | `00`              |                              |
| 18       | `00`              |                              |


The message of type `39` or `40` has the following format:

| Position | Value             | Details                      |
| -------- | ----------------- | ---------------------------- |
| 0        | `39` or `40`      |                              |
| 1        | `00` - `FF`       | Heartrate (bpm)              |
| 2 - 3    | `00 00` - `FF FF` | Step count                   |
| 4 - 5    | `00 00` - `FF FF` | Ten minutes                  |
| 6        | `00`              |                              |
| 7        | `00`              |                              |


The message of type `52` has the following format:

| Position | Value             | Details                      |
| -------- | ----------------- | ---------------------------- |
| 0        | `52`              |                              |
| 1        | `00` - `FF`       | Heartrate (bpm)              |
| 2 - 3    | `00 00` - `FF FF` | Step count                   |
| 4        | `00` - `FF`       | Ten minutes                  |
| 5        | `00` - `FF`       | Heartrate (bpm)              |
| 6 - 7    | `00 00` - `FF FF` | Step count                   |
| 8        | `00` - `FF`       | Ten minutes                  |
| 9        | `00` - `FF`       | Heartrate (bpm)              |
| 10 - 11  | `00 00` - `FF FF` | Step count                   |
| 12       | `00` - `FF`       | Ten minutes                  |
| 13       | `00`              |                              |
| 14       | `00`              |                              |
| 15       | `00`              |                              |
| 16       | `00`              |                              |
| 17       | `00`              |                              |
| 18       | `00`              |                              |


All data that spans multiple bytes, such as the step count is send in little-endian format. That means a step count of 500, which would be `01F1` in hexadecimal notation would be send as `F1 01`.

When the hearrate sensor can't get a good reading, it will send `FF` as the value, which can best simply be ignored and considered a non-value.