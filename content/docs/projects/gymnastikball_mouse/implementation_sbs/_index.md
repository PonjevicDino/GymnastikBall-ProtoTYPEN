---
title: Step-by-Step implementation
type: docs
---

# Implementation (Step-by-Step)

To start with the building of our prototype, we needed a materical list at first, which consists of the following items:
* 1 pcs: Gymnastic ball (of course..., model unknown)
* 1 pcs: Microcontroller (*ESP32 Feather*)
* 2 pcs: Gyroscopic sensors (*ICM-20948*)
* ... and some consumable material (ie. prototyping PCBs, cables, etc.) 

## Step №0: Getting the materials. 

At the beginning, we thought of an implementation with four gyroscopic sensors and an *Arduino UNO*. To accomplish this, we ordered the MPU6050 gyros from
[conrad.at](https://www.conrad.at/de/p/joy-it-mpu6050-beschleunigungssensor-1-st-passend-fuer-entwicklungskits-bbc-micro-bit-arduino-raspberry-pi-rock-pi-2136256.html)
directly at the start of the prototyping phase. During the processing of the order, which took a few days, the Media Interaction Lab Team provided us two
*ICM-20948* gyros and a *ESP32 Feather* as the corresponding microcontroller for testing. Furthermore, the ESP32 has a integrated Bluetooth module, which
will become handy at a later point. Finding a second hand gymnastic ball near Hagenberg was quite easy (we got it for free).

{{< figure src="gyro.jpg" width="50%" caption="*ICM-20948*">}}
{{< figure src="esp32.jpg" width="50%" caption="*ESP32 Feather*">}}

## Step №1: Connecting the parts together (... and hope, that it doesn't explode)

None of us had real experience in soldering before the prototyping courses. An other "problem" was the small size of the pins and PCB-holes. To make the
process easier, we connected one gyro via header pins to a larger prototyping PCB. The same steps were also done for the *ESP32*. The connection between
the microcontroller and the gyro was established (via cables) with the following pins (the PinLayout of the ICM-20948 gyro can be seen in Step №0):
| Pin on ESP32 | Pin on ICM-20948 | Explanation                                                                           |
| ----         | ----             | :----                                                                                 |
| 5V           | VIN              | Provides 5V power. The gyro has a step-down converter, which changes the 5V to 3V-DC. |
| GND          | GND              | Ground. Completes the power circuit.                                                  |
| CLK          | SCL              | Clock signal for the I2C logic.                                                       |
| SDA          | SDA              | Data return channel for the I2C logic.                                                |

Afterwards we then provided power to the microcontroller via the USB Port and a small red LED-light on the gyroscope indicates, that it is providing data.
No smoke, no fire and even the laptop didn't shut down ... good!

{{< figure src="soldering.JPEG" width="100%" caption="*Soldering*">}}
{{< figure src="wiring.JPEG" width="100%" caption="*Wired together*">}}

## Step №2: Get the data from the gyroscope

Now, that we had a working hardware connection, we needed to write some code, to get the values from the gyro-sensors. Thankfully, there is a "Quick Setup" 
Tutorial on the [Adafruit Website](https://learn.adafruit.com/adafruit-tdk-invensense-icm-20948-9-dof-imu/arduino), where the needed libraries are shown and 
some example code is provided. Even if the code is originally meant for the Arduino, we tried it on the ESP32 Feather ... and we got some data! The formating 
of the serial output was quite bad, but that could be changed later.

## Step №3: Find the right data

To send the correct movements to the mouse-library, we needed to get the current rotation of each sensor around each axis. To accomplish this, a gyroscope 
is normally used, which initialises itself at startup and measures the difference between the current and the initial rotations. The *ICM-20948* has four 
sensors on board, an accelerometer, a gyroscope, an angular velocity sensor and a temperature sensor. Sadly, the gyroscope provided confusing data, beacuse 
it did not initialize properly at startup and the values got reset to zero after a short time without any movement. After some brainstorming on how to get 
the current rotation of the sensor, we had the idea to find a parameter, which is always the same, regardless of the current rotation ... **gravity!**

{{< figure src="brainstorming.JPEG" width="100%" caption="*Heavy brainstorming work*">}}

## Step №3.1: Gravity + accelerometer = ❤

An accelerometer measures the force, which is changing the movement for every axis. And because the earths gravity pulls down on everything with around 
**9.81m/s²**, we had our value, which couldn't change. We just need to measure, on which axis the gravity is applied.

## Step №3.2: Filtering the data

This step was quite simple. We studied the code a bit more and found the variables, which get overwritten with the new sensor values for each clock cycle.
At the end, we had one variable ... **a three dimensional vector (x, y, z)**.

## Step №4: Connecting the second ICM-20948

We've done step №1 again, until we reached that part, where we needed to connect the cables to the ESP32 again. 5V-DC and ground were simple, because we 
didn't reach the current limit of the microcontroller. The clock signal could also be sent to both sensors at the same time via the same pin, but the return 
channel was the problematic one. There was a way using the *STEMMA QT* protocoll, which provides the ability to daisy-chain multiple sensors together, but 
this required spectial cables/connectors and the time was running. So we needed to connect both sensors to the same pins via a "Y-Splitter" connection.

{{< figure src="daisy-chain.JPEG" caption="*Daisy-Chaining*">}}

## Step №4.1: The I2C protocoll ... simplified

As shown in the step before, we had three of four connections working. Every device, which uses the I2C protocoll (and thus the ICM-20948) needs its own 
address, so that the master device (in our case the ESP32) can seperate the data on the return channel. At default the sensor uses the address *0x69*, but
there was also a way to change it.

## Step №4.2: Shorting a pin on purpose

The documentation of the *ICM-20948* states, that the chip/sensor can change its address to *0x68* by making a hardware change. It is as simple as shorting
the address pin to ground. Some searching later, we've found the "pin", which was more like a surface and was located on the back side of the sensor (who 
would put it there?). And after putting way too much solder on the contacting surfaces, we've created what can be considered as a irreversable address change. 
But after connecting it to the *ESP32* it worked!

## Step №5: Back to the software

After connecting the *ESP32* back to the laptop, we didn't have a fire, but the same data as before (only for sensor *0x69* which will be written as *first* 
sensor from now on). The example code initialises an I2C connection with the default address at startup. By simply copying this code snippet and changing the
address to *0x68* (for the *second* sensor), we could redirect the data to an another variable. At this point, we had two vectors, one with values for the 
three axis from each sensor.

## Step №6: Mapping the data to a mouse input

The user *T-vK* on GitHub created and shared a mouse library, which uses the *ESP32* Bluetooth controller to simulate a computer mouse. The connected device 
i.e. a laptop or a phone therefore cannot distinguish between a "normal" mouse and the *ESP32*. That's why, we just needed to send the movements to the 
corresponding function and the cursor will move reliably.
```c++
void setup(void) {
	[...]
	bleMouse.begin();
	[...]
}

void loop() {
	[...]
	if(bleMouse.isConnected()) bleMouse.move(x,y);
	[...]
}
```

## Step №7: Calculate x and y

At the beginning we had the idea to use one *ICM-20948* for the horizontal movement, the other for the vercial and both together for the click-calculation. 
We then mapped the X- and the Y-Axis from sensor one directy to the *bleMouse.move*-function for testing and the mouse cursor was directly moving quite well.
After that, we changed our approach to using sensor one for the movement and sensor two solely for clicking.
{{< video src="mouse.mp4" caption="*First mouse movement*">}}

## Step №8: Soldering rework and mounting everything to the gymnastic ball

Before we could mount the sensors and the *ESP32* to the (moving) gymnastic ball, we needed to consider one problem - the length, stiffness and count of 
cables. Furthermore, we would need to improve the connection points to withstand the force while everything is moving. To do that, we've got an *XLR*-cable, 
which has six wires inside, everyone isolated from each other and a "global" isolation layer. Then we measured the needed length of the cables, cut them open 
and resoldered everything back together, not noticing, that we've created a short circuit on one of the sensors. Two cables were touching each other next to 
the soldering point, ... but that's nothing a bit of duct tape cannot fix.
{{< figure src="prototype-v1.JPEG" width="100%" caption="*Lukas is happy with our prototype V1*">}}

## Step №9: Optimizing the software

Because we mounted the first sensor to the back of the gymnastic ball, the orientation had changed. Therefore we needed to remap the axis of the accelerometer 
to the mouse function. At the end, it was the **Y-Axis** for the **X**-Movement and the the **Z-Axis** for the **Y-Movement** of the mouse cursor. We also 
created a "smoothing"-function to prevent jittering, a dead-spot zone and a sensitivity variable.

## Step №10: Click, click

The second sensor, which **Z**-Movement is responsible for triggering a click, was mounted also on the side of the ball at the beginning. It should measure 
the squeezing and therefore the horizontal expansion of the gymnastic ball. But the sensor was not able to distinguish between a click and a rapid change in 
horizontal movement, and if we set sensitivity to a low value, you would have to jump quite "fast". The top point of the ball moves at most, when a click-
action is done, therefore we mounted the second sensor near to this point but without interfering with the participants movement.

## Step №11: 3D-Printing

To be able to do the "finetuning" of the software, we needed to have the sensors at the exact same position at every run. Duct tape was not a satisfactory 
result, because it kept falling off. We then printed two "boxes" for the *ICM-20948*s and one for the *ESP32*, all three with a round mounting surface, which 
could be glued to the gymnastic ball.
{{< figure src="3D_click.JPG" caption="*Box for ICM-20948*">}}
{{< figure src="3D_ESP_battery.JPG" caption="*Box for ESP32 + for the power bank*">}}
{{< figure src="glueing.JPEG" width="100%" caption="***Question:** How many fingers did we need to glue it together? **Answer:** 14!*">}}
{{< figure src="final.jpg" width="100%" caption="*Final Prototype*">}}

## Step №12: Optimizing and finetuning the software ... again

With the new fixed sensor positions we could calculate the click movement. The final function calculates the difference of each of the last ten sensor 
inputs between its average. If the value is over a set threshold and the last click was a fixed time ago, it would trigger a click. In this way, the 
triggering is also less dependend on the participants weight then if the sensor would be mounted on the side.

## Step №12: What to show at the presentation?

To do some "work" in the Windows-Environment, the input fields were quite small and not triggerable in a short period of time. A full fledged game was also 
not a solution, because then we needed to explain the controls to everyone individually. Therefore, we created a simple "accuracy" WebTool, in which a 
circle (with decreasing size) appears every few seconds and the participant had to click it as fast as possible. The count of the clicked circles is then 
saved in a simple scoreboard. Finally to find the mouse at the begin of each "game", we created a simple Python program, which resets the mouse cursor to 
the middle of the screen, when a specific key is pressed. - In our case, the **CTRL** key.

{{< video src="presentation.mp4" caption="">}}