# Proximity Based Garage Door Access
**Team members:** Dhruva Devasthale, Prabhleen Kaur Gill, Vandita Shetty

**Georgia Institute of Technology**

Link to project demo: 


## Project Idea

In today's world, securing access to personal or society garages is very important. So, we have designed a garage door access system secured by password to prohibit or grant accessing to cars trying to enter private properties. 
The image below shows the block diagram of our project.

![Block diagram](https://github.com/vshetty33/ECE4180-final-project/blob/main/Mbed_Block_Diagram%20(1).jpg)

Block Diagram of our device

The system is designed to perform the following tasks:

1. When a car comes within 300mm of the gate (distance sensor), it prompts the user for Entering Password.
2. The Password is a 4 digit key.
3. The user enters the password using blutooth controlled keypad.
4. If the password is correct, the RGB LED turns green and the barrier opens. After the car has passed, the barrier comes down automatically.
5. If the password is incorrect, the RGB LED turns Red and a beep is sounded using the speaker.
6. The user is prompted to reenter the password. They are given 3 warnings and one final try.
7. If the user inputs incorrect password on the fourth try, an alarm starts sounding and the system resets.
8. The user is denied access.

## Parts List

Electronics:

* Mbed LPC1768, https://www.sparkfun.com/products/9564
* LCD Display: uLCD-144G2, https://www.sparkfun.com/products/11377
* ToF Distance Sensor, https://www.adafruit.com/product/3317
* Servo: HS-442, https://www.servocity.com/hs-422-servo
* Speaker: https://www.sparkfun.com/products/11089
* Class D Amplifier, https://www.sparkfun.com/products/11044
* Resistors: 330ohm, https://www.sparkfun.com/products/14490
* Bluetooth module, https://www.adafruit.com/product/2479
* uSD Card File System and Breakout, https://www.sparkfun.com/products/544
* RGB LED, https://www.sparkfun.com/products/105
* 5V External Power Supply
 

Non Electronics: 

* Cardboad box for depicting a car
* Cardboard strap for depicting servo controlled barrier

## Schematic Diagram and Pin Connections
This section provides the circuit diagram and connections needed for implementation.

![Pin Connections](https://github.com/vshetty33/ECE4180-final-project/blob/main/PinConnectionL.jpeg)

![Pin Connections](https://github.com/vshetty33/ECE4180-final-project/blob/main/PInConnectionD.jpeg)

## Connection Table for all components

| MBED | ToF Distance | Servo | uLCD | BLE | SD breakout | Class D Amp | RGB LED |
| ---- | ------------ | ---- | --- | ----------- | ----- | ----------- | ------- |
| Vdd = 5V | | 5V | Vin | | |
| Vdd = 3.3V | Vin | | | | Vcc | Pwr+ | 
| GND | Gnd | Black | Gnd | Gnd,Cts | Gnd | Pwr-, IN- | Gnd |
| p5 | | | | | DI |
| p6 | | | | | DO |
| p7 | | | | | SCK |
| p8 | | | | | CS |
| p9 | | | | TX | |
| p10 | | | | RX | |
| p11 | | | Reset | | |
| p13 | | | Tx | | |
| p14 | | | Rx | | |
| p16 | | | | | | | red | 
| p18 | | | | | | IN+ |
| p19 | | | | | | | green |
| p21 | | yellow |
| p26 | xshut | 
| p27 | scl |
| p28 | sda |

Connections between Speaker and Class D Amplifier

| Speaker | Class D Amp |
| ------- | ----------- |
| + | Out+ |
| - | Out- |

![Bluetooth Controls](https://github.com/vshetty33/ECE4180-final-project/blob/main/BLE_diagram.jpg)

Our bluetooth control screen
