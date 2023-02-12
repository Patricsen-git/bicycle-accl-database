# Bicycle IMU Database

This is a collection of readings from an IMU, while different bicycle scenarios were performed. Three different scenarios were done:
- Stationary
- Moving, including accelerating, braking and taking turns, without full stops
- Falling, either to the left or right

It includes exclusive readings from an accelerometer and also readings from both an accelerometer and a gyroscope. They are differentiated by the folder structure, `6-axis` contain measurements from both sensors, while `3-axis` feature the accelerometer alone.  


## Example Usage
The readings are saved as a .csv file. The structure is as follows:
| 1. X-Accl. | 1. Y-Accl | 1. Z-Accl | 1. X-Ang. Vel. | 1. Y-Ang. Vel. | 1. Z-Ang. Vel. |
|------------|-----------|-----------|----------------|----------------|----------------|
| 2. X-Accl. | 2. Y-Accl | 2. Z-Accl | 2. X-Ang. Vel. | 2. Y-Ang. Vel. | 2. Z-Ang. Vel. | 
| 3. X-Accl. | 3. Y-Accl | 3. Z-Accl | 3. X-Ang. Vel. | 3. Y-Ang. Vel. | 3. Z-Ang. Vel. |
etc...

**Note:** Angular velocity samples are only present in the folder `6-axis`.  

The files can either be loaded individually with something like `NumPy` or `Pandas`:


``` python
data = np.loadtxt("stop_3_00.csv", delimiter=",")
```
Or they can be loaded into a Dictionary, labeled by their filename.
``` python
import os
import pandas as pd

dataset = dict()
for filename in os.listdir("3-axis"):
    dataset[filename] = pd.read_csv("3-axis/" + filename, header=None).to_numpy()
```
Note that these are just two exemplary methods of loading the data. Depending on the intented usage, different methods may be used.

## The three classes:
### Stationary:
While the bicycle is locked and not used, which it is for an average user most of the time, it is only slightly or not moving at all. This is called the stopped or stationary state. The files are labelled as follows:

`stop_X_YY.csv`

**X** is either 3 or 6, indicating whether the data is exlusively from the accelerometer or the Gyroscope was also utilized. **Y** is the recording number.

### Moving
While actively using a bicycle, the user either moves or is stationary. Moving describes a lot of actions, such as accelerating, braking, steering or any combination of these three. However, the exact movement is not labelled.

The files are labelled as follows:

`moving_X_YY.csv`

**X** is either 3 or 6, indicating whether the data is exlusively from the accelerometer or the Gyroscope was also utilized. **YY** is the recording number.

# Falling
Detecting whether the bike is falling to the left or the right, also just called the falling state. 

The files are labelled as follows:

`falling_ZZZZ_X_YY`

**X** is either 3 or 6, indicating whether the data is exlusively from the accelerometer or the Gyroscope was also utilized. **Y** is the recording number. In some cases, the specific falling direction was also noted. If it is, **ZZZZ** indicated, whether the bicycle was falling left or right. Otherwise it is left out.

## How was the data collected?
### Hardware:
The [IMU](https://content.arduino.cc/assets/Nano_BLE_Sense_lsm9ds1.pdf) is integrated on the [Arduino Nano 33 BLE Sense](https://docs.arduino.cc/resources/datasheets/ABX00031-datasheet.pdf). The Arduino got tightly mounted on the bicycle rack with cable ties. A recording frequency of 10 Hz was chosen, meaning 30 (60 if the Gyroscope is also active) values, 10 for each axis, were taken per second. Since the non-volatile memory of the Arduino is not very large, the data was directly transferred to a laptop via cable, enabling to record for extended periods of time. The laptop was carried in a basked or in a backpack. 

### Software:
The Arduino script is straightforward. The readings are printed to the Serial Monitor, as soon as the Arduino initialized. The first sent message indicated, that the measurements have started. This enabled us to have multiple recordings in a row, each seperated by the first message. Once the Motion has finished, the Arduino was reset, which indicated that the recording has finished. Afterwards the readings were saved manually to a .csv file with the aid of ArduSpreadsheet, available [here](https://circuitjournal.com/arduino-serial-to-spreadsheet).

### Simulating the Scenarios:
All recordings were done outside to capture natural environment. 
For the stationary State it is quite easy, since the bicycle can be leaned against bicycle stands, a wall or can rest on the kickstand. 

Simulating moving was done by riding different routes outside while avoiding full stops. However, slowing down, accelerating and taking turns were all included. Additionally, different people rode the same route to gather a variety of samples.

Falling is the most difficult one to simulate. Taking a convincing fall while riding the bike poses a threat not only for the rider, but also for the bicycle itself. Therefore, the fall started from a stationary position. While not harming the rider in this case, the bicycle still gets damaged if it falls multiple times. To prevent damage, the bicycle was caught by a person just before touching the ground. While not providing perfectly accurate data, this was a necessary compromise to mitigate bicycle damage. In a more professional scenario, an old, already damaged bicycle could be used, where damage is not a concern.
