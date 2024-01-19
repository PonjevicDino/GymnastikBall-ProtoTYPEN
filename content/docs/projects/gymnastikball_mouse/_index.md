---
title: Gymnastic ball as a mouse
type: docs
---

# Gymnastic ball mouse input

## Abstract

The Gymnastic Ball Mouse, a novel project by us, The ProtoTypen team, merges the practicality of a computer mouse with the physicality of a gymnastic ball. 
Aimed at enhancing user-computer interaction, this prototype was designed to simplify the switch between keyboard and mouse and to introduce an ergonomic 
and active element to the computer workspace. It operates on the principle of weight shifts on the ball translating into cursor movements, with a unique 
bounce feature for clicking. The core technology comprises gyro sensors and an ESP32 microcontroller, intricately programmed to translate physical 
movements into digital cursor control with precision. This project not only challenged our technical skills, particularly in sensor data interpretation 
and wireless communication, but also fostered creative problem-solving. Culminating in a playful yet functional device, the Gymnastic Ball Mouse 
exemplifies our innovative approach to combining technology with physical activity for an engaging computer experience.

## Introduction

We set out on the challenging task to develop the Gymnastic Ball Mouse, aiming to merge the practicality of a computer mouse with the physical engagement 
of a gymnastic ball. This project was driven by the goal of simplifying user interaction with computers, particularly in tasks requiring frequent 
alternation between keyboard and mouse, and introducing an ergonomic, active element to the computer-using experience. 
Therefore, the "ProtoTypen"-Team introdudes the

**Gymnastic ball as an mouse input!** - We didn't find another name...

It completes the task above, but creates many problems at the same time, i.e. decreased pointer accuracy any many *"Where is my cursor?"* moments.

-> Chindōgu approved!

## Related work 

A similar prototype, created by *Beckhaus Steffi*, *Blom J. Kristopher* and *Matthias Haringer*, which takes the position ("bending") and the rotation of a
chair and maps it to an input (similar as joysticks) for games, already exists. -> **ChairIO**

The main difference between the ChairIO and our "Gymnastic ball mouse" is the rotation axis. While the ChairIO can also track and interpret the rotation
of the chair, our "third Axis" is solved with a vertical motion. With this, we can trigger a mouse click without any seperate buttons or any modifications
within the software, which is running on the microcontroller. Meanwhile, the ChairIO uses foot switches for the both mouse buttons respectively.

The full paper of the **ChairIO** can be found [here](https://www.researchgate.net/publication/233819716_ChairIO--the_Chair-Based_Interface).

## Implementation 

A fully step-by-step timeline of the implementation of the project can be found [here]({{< ref "implementation_sbs" >}})

### Concept Development:

Our development journey began with the aim of reducing the repetitive motion of switching between keyboard and mouse during computer usage. We explored 
various ideas, such as foot-operated mice and integrating mouse functionality into office chair movements. Eventually, we decided on a unique and 
challenging concept: using a gymnastic ball as a mouse. The idea was to control cursor movement through the user's shifting weight on the ball – left and 
right for horizontal motion, forward and backward for vertical motion. A distinctive feature was the bouncing action on the ball to execute a mouse click, 
adding a playful / annoying yet functional aspect to the device.

Our early designs explored different mechanisms, including large wheel-based systems and dance mat-inspired interfaces. Ultimately, we chose to use 
gyroscopic sensors for capturing the ball's movements, with a wireless ESP32 microcontroller to process and transmit this data.

### Technical Implementation:

In our final design, we opted for gyro sensors and an ESP32 microcontroller. The core components were two ICM-20948 sensors from Adafruit, which provided 
multi-faceted data including gyroscopic movement, acceleration, and magnetic field detection. We primarily utilized the accelerometer data, with a focus 
on the z-axis, due to its reliable response to gravitational forces, crucial for determining the ball's orientation and movement.

Click detection presented a notable challenge. Our initial attempts at using simultaneous data shifts from both sensors proved inconsistent. We then 
implemented an additional sensor, strategically placed near the seating area, to enhance the detection of the jumping or clicking motion.

### In-Depth Software Development:

Programming the ESP32 was undertaken using the Arduino IDE. We utilized Adafruit's library to access and interpret the sensor data effectively. The 
critical task was converting this data into precise mouse movements. To accomplish this, we leveraged the "ESP32 BLE Mouse" library, enabling efficient 
Bluetooth communication between the ball and the computer.

We encountered initial difficulties with erratic cursor movements and hypersensitivity. To address these, we fine-tuned the sensor data sampling rate, 
increasing it to ensure smoother cursor movement. A key addition to our software was the implementation of a sensitivity control setting, allowing users 
to adjust the responsiveness of the mouse to their movements.

Another significant improvement was the introduction of a 'dead center' threshold. This was designed to combat unintended cursor drift – if the sensor's 
output was within this minimal threshold, the cursor would remain stationary, preventing unwanted movement when the user intended stillness.

Enhancing click detection required a nuanced approach. We developed an algorithm to track and analyze the recent sensor readings for the click action. By 
averaging these readings and setting a calibrated threshold, we could more accurately determine intentional clicks. Post-click, we implemented a brief 
cursor freeze, reducing cursor jitter and enhancing the overall user experience.

### 3D Printing and Assembly:

The initial phase of attaching sensors to the ball using tape was quickly deemed unsuitable. We transitioned to designing and 3D printing custom mounts 
for the sensors, microcontroller, and power bank. These components were carefully engineered for flexibility, using a low-profile plastic bed to ensure 
they conformed to the ball's curvature and movements. Post-printing, we refined the mounts with sandpaper for better surface adhesion, and used 
specialized glue to attach them securely to the ball.

### Game Development for User Testing:

To validate and demonstrate the functionality of our prototype, we created a simple, interactive game. This game involved users clicking on progressively 
shrinking circles displayed on the screen, challenging them to adapt to the ball mouse’s unique control mechanism. The game not only served as a fun 
testing platform but also allowed us to gather valuable feedback on user experience and the device's performance.

### Materials, Tools, and Budget:

Our project was resource-efficient, thanks to the provision of major components by our university. The sensors and microcontroller were provided at no 
cost, and we acquired the gymnastic ball for free on Willhaben.at. The main expenses were for 3D printing materials, sandpaper, and glue, totaling around 
€20. Supplementary items such as a power bank and minor hardware were also sourced without additional cost.

## Conclusion

The Gymnastic Ball Mouse project was a fusion of technical challenge and creative problem-solving. It enriched our understanding of sensor technology, 
wireless communication, and ergonomic design principles. Despite its unconventional approach, the project was a rewarding and educational journey, ending 
in a unique prototype that could have potential use cases in the future of computer peripherals and something we as a team are really proud of.