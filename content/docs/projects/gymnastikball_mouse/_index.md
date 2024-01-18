---
title: Our project name
type: docs
---

# Gymnastic ball Mouse Input

## Abstract

*TODO: A short and concise description of your project.*

## Introduction

Everybody knows, that working on a computer for a long period of time is not healthy for the body. At the same time, you can try to do the work faster,
to spend less time in front of the PC. For example, many people use a version of the "10 finger" typing system, to improve the typing speed on the keyboard,
but often you need to work also with the mouse, which prevents you from typing. Why not combine both Problems and find a perfect solution?
Therfore, the "ProtoTYPEN"-Team introdudes the ...

**Gymnastic ball as an mouse input!**

It solves the above problem, but creates many more at the same time, i.e. decreased pointer accuracy any many *"Where is my cursor?"* moments.

-> Chindōgu approved!

## Related work 

A similar prototype, created by *Beckhaus Steffi*, *Blom J. Kristopher* and *Matthias Haringer*, which takes the position ("bending") and the rotation of a
chair and maps it to an input (similar as joysticks) for games, already exists. -> **ChairIO**

The main difference between the ChairIO and our "Gymnastic ball mouse" is the rotation axis. While the ChairIO can also track and interpret the rotation
of the chair, our "third Axis" is solved with a vertical motion. With this, we can trigger a mouse click without any seperate buttons or any modifications
within the software, which is running on the microcontroller. Meanwhile, the ChairIO uses foot switches for the both mouse buttons respectively.

The full paper of the **ChairIO** can be found [here](https://www.researchgate.net/publication/233819716_ChairIO--the_Chair-Based_Interface).

## Implementation 

*TODO: A detailed description of your prototyping process.*
To start with the building of our prototype, we needed a materical list at first, which consists of the following items:
* 1 pcs: Gymnastic ball (of course..., model unknown)
* 1 pcs: Microcontroller (*ESP32 Feather*)
* 2 pcs: Gyroscopic sensors (*ICM-20948*)
* ... and some consumable material (ie. prototyping PCBs, cables, etc.) 

### Step №0: Getting the materials. 

At the beginning, we thought of an implementation with four gyroscopic sensord and an *Arduino UNO*. To accomplish this, we ordered the MPU6050 gyros from
[conrad.at](https://www.conrad.at/de/p/joy-it-mpu6050-beschleunigungssensor-1-st-passend-fuer-entwicklungskits-bbc-micro-bit-arduino-raspberry-pi-rock-pi-2136256.html)
directly at the start of the prototyping phase. During the processing of the order, which took a few days, the Media Interaction Lab Team provided us two
*ICM-20948* gyros and a *ESP32 Feather* as the corresponding microcontroller for testing. Furthermore, the ESP32 has a integrated Bluetooth module, which
will become handy at a later point. Finding a second hand gymnastic ball near Hagenberg was quite easy (we got it for free).

### Step №1: Connecting the parts together (... and hope, that it doesn't explode)

None of us had real experience in soldering before the prototyping courses. An other "problem" was the small size of the pins and PCB-holes. To make the
process easier, we connected one gyro via header pins to a larger prototyping PCB. The same steps were also done for the *ESP32*. The connection between
the microcontroller and the gyro was established (via cables) with the following pins:
*TODO: Insert Photo of the Gyro PinLayout*
| Pin on ESP32 | Pin on ICM-20948 | Explanation                                                                           |
| ----         | ----             | :----                                                                                 |
| 5V           | VIN              | Provides 5V power. The gyro has a step-down converter, which changes the 5V to 3V-DC. |
| GND          | GND              | Ground. Completes the power circuit.                                                  |
| CLK          | SCL              | Clock signal for the I2C logic.                                                       |
| SDA          | SDA              | Data return channel for the I2C logic.                                                |

Afterwards we then provided power to the microcontroller via the USB Port and a small red LED-light on the gyroscope indicates, that it is providing data.
No smoke, no fire and even the laptop didn't shut down ... good!

### Step №2: Get the data from the gyroscope

Now, that we had a working hardware connection, we needed to write some code, to get the values from the gyro-sensors. Thankfully, there is a "Qucik Setup" 
Tutorial on the [Adafruit Website](https://learn.adafruit.com/adafruit-tdk-invensense-icm-20948-9-dof-imu/arduino), where the needed libraries are shown and 
some example code is provided. Even if the code is originally meant for the Arduino, we tried it on the ESP32 Feather ... and we got some data! The formating 
of the serial output was quite bad, but that could be changed later.

### Step №3: Find the right data

To send the correct movements to the mouse-library, we needed to get the current rotation of each sensor around each axis. To accomplish this, a gyroscope 
is normally used, which initialises itself at startup and measures the difference between the current and the initial rotations. The *ICM-20948* has four 
sensors on board, an accelerometer, a gyroscope, an angular velocity sensor and a temperature sensor. Sadly, the gyroscope provided confusing data, beacuse 
it did not initialize properly at startup and the values got reset to zero after a short time without any movement. After some brainstorming on how to get 
the current rotation of the sensor, we had the idea to find a parameter, which is always the same, regardless of the current rotation ... **gravity!**

### Step №3.1: Gravity + accelerometer = ❤

An accelerometer measures the force, which is changing the movement for every axis. And because the earths gravity pulls down on everything with around 
**9.81m/s²**, we had our value, which couldn't change. We just need to measure, on which axis the gravity is applied.

### Step №3.2: Filtering the data

This step was quite simple. We studied the code a bit more and found the variables, which get overwritten with the new sensor values for each clock cycle.
At the end, we had one variable ... **a three dimensional vector (x, y, z)**.

### Step №4: Connecting the second ICM-20948

We've done step №1 again, until we reached that part, where we needed to connect the cables to the ESP32 again. 5V-DC and ground were simple, because we 
didn't reach the current limit of the microcontroller. The clock signal could also be sent to both sensors at the same time via the same pin, but the return 
channel was the problematic one. There was a way using the *STEMMA QT* protocoll, which provides the ability to daisy-chain multiple sensors together, but 
this required spectial cables/connectors and the time was running.

### Step №4.1: The I2C protocoll ... simplified

As shown in the step before, we had three of four connections working. Every device, which uses the I2C protocoll (and thus the ICM-20948) needs its own 
address, so that the master device (in our case the ESP32) can seperate the data on the return channel. At default the sensor uses the address *0x69*, but
there was also a way to change it.

### Step №4.2: Shorting a pin on purpose

The documentation of the *ICM-20948* states, that the chip/sensor can change its address to *0x68* by making a hardware change. It is as simple as shorting
the address pin to ground. Some searching later, we've found the "pin", which was more like a surface and was located on the back side of the sensor (who 
would put it there?). And after putting way too much solder on the contacting surfaces, we've created what can be considered as a irreversable address change. 
But after connecting it to the *ESP32* it worked!

### Step №5: Back to the software

After connecting the *ESP32* back to the laptop, we didn't have a fire, but the same data as before (only for sensor *0x69* which will be written as *first* 
sensor from now on). The example code initialises an I2C connection with the default address at startup. By simply copying this code snippet and changing the
address to *0x68* (for the *second* sensor), we could redirect the data to an another variable. At this point, we had two vectors, one with values for the 
three axis from each sensor.

### Step №6: Mapping the data to a mouse input

## Conclusion

*TODO: A reflection on your prototyping process and the project outcome. What happens to the prototype after the project?*