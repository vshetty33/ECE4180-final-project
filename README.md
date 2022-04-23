# Proximity Based Garage Door Access
**Team members:** Dhruva Devasthale, Prabhleen Kaur Gill, Vandita Shetty

**Georgia Institute of Technology**

Link to project demo: 

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

![https://github.com/vshetty33/ECE4180-final-project/blob/main/Mbed_Block_Diagram%20(1).jpg]




















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
 


