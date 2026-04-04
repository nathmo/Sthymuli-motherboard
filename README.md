# Sthymuli-motherboard
This repo contains the PCB for the Sthymuli motherboard. Sthymuli is an educative robot aimed at children whith the aim of promoting collaborative learning.

The motherboard is designed arround a doughnut shape with addons that plug in the center. the shape was chosen to encourage equal access to up to four children at a time interracting with the device.

the circuit is splitted between one motherboard and three cardinalboard 
this repo has the motherboard
<img width="1600" height="1217" alt="image" src="https://github.com/user-attachments/assets/c4e661e0-f9ae-48d2-bfc9-3e2ef1e253e7" />

the cardinal board can be found there : https://github.com/nathmo/Sthymuli-cardinalboard
<img width="1324" height="656" alt="image" src="https://github.com/user-attachments/assets/6bce58a5-3221-47e1-8b61-8c7d59fe262c" />

# Feature
The device contains the following sensors 

===

* # USB-C connector
* # Batterie + BMS
* # ESP32 + STM32
* # Speaker
* # Microphone
* # capacitive buttons (distributed arround the doughnut as discussed last time)
* # IR remote receive
* # 4 IR proximity Sensor
* # Motor Driver (so they can just connect the two wire of the motor and dont need to add a Hbridge on the dauther board
* # RGB LED (there I need to count how many and decide with you what we keep and what we breakout for the daugther board



# Daugther boards

the following will be available for the daugther board :


* IMU
* color sensor
* 2 DC motor channel
* LEDs
* IR proximity sensor / analog pin that can be used for sensing

