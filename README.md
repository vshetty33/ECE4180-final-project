# Proximity Based Garage Door Access
**Team members:** Dhruva Dilip Devasthale, Prabhleen Kaur Gill, Vandita Shetty

**Georgia Institute of Technology**

**ECE 4180 Final Project**

Link to project demo: https://www.youtube.com/watch?v=iIJetakIFtA

## Table of Contents
* Project Idea
* Parts List
* Schematic Diagram and Pin Connections
* Connection Table for all components
* Source Code
 


## Project Idea

In today's world, securing access to personal or society garages is very important. So, we have designed a garage door access system secured by password to prohibit or grant accessing to cars trying to enter private properties. 
The image below shows the block diagram of our project.

![Block diagram](https://github.com/vshetty33/ECE4180-final-project/blob/main/pics/Mbed_Block_Diagram%20(1).jpg)

Block Diagram of our device

The system is designed to perform the following tasks:

1. When a car comes within 300mm of the gate (distance sensor), it prompts the user to Enter the Password.
2. The Password is a 4 digit key.
3. User enters the password using blutooth controlled keypad.
4. If the password is correct, RGB LED turns green and the barrier opens. After the car has passed, the barrier comes down automatically.
5. If the password is incorrect, RGB LED turns Red and a warning beep is sounded using the speaker.
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

![Pin Connections](https://github.com/vshetty33/ECE4180-final-project/blob/main/pics/PinConnection.jpeg)

![Pin Connections](https://github.com/vshetty33/ECE4180-final-project/blob/main/pics/PinConnection1.jpeg)

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

![Bluetooth Controls](https://github.com/vshetty33/ECE4180-final-project/blob/main/pics/BLE_diagram.jpg)

Our bluetooth control screen

## Source Code

//////////////////////////////////////////////////////////////////////////
//PROJECT: Proximity based garage door access                           //
//TEAM: Dhruva Dilip Devasthale, Prabhleen Kaur Gill, Vandita Shetty    //
//ECE 4180 (Section A)                                                  //
//////////////////////////////////////////////////////////////////////////

// Include Files
#include "mbed.h"
#include "SDFileSystem.h"
#include "wave_player.h"
#include "XNucleo53L0A1.h"
#include "uLCD_4DGL.h"
#include "string"
#include "Servo.h"
#include <stdio.h>

// Serial mbed port
Serial pc(USBTX,USBRX);

// This VL53L0X (ToF distance sensor) board test application performs a range measurement in polling mode
// XSHUT of ToF Distance Sensor
DigitalOut shdn(p26);

// LCD connections
uLCD_4DGL uLCD(p13,p14,p11); // serial tx, serial rx, reset pin;

// RGB LED red and green pins used
DigitalOut redpin(p16);
DigitalOut greenpin(p19);

// SD card connections
SDFileSystem sd(p5, p6, p7, p8, "sd"); //SPI mosi, SPI miso, SPI clk, CS

// Analog output for class D amp
AnalogOut DACout(p18);
wave_player waver(&DACout);     // Waver for playing audio files

// Servo motor controlling the garage door
Servo myservo(p21);

// Bluetooth RX, TX
Serial blue(p9,p10);

//I2C sensor pins for ToF distance sensor
#define VL53L0_I2C_SDA   p28
#define VL53L0_I2C_SCL   p27

static XNucleo53L0A1 *board=NULL;

bool flag = false;          // checking for car
int warn_count = 0;         // Each user given three trials to enter correct password
bool alarm_flag = false;    // SOS alarm goes off when user enters three wrong passwords

// main function
int main()
{
    // bluetooth by default takes a character input entered by the user for two continuous times. 
    // We created a count variable to read it only once and ignore the second time
    int count =0;           
    
    // We have used a 4 digit numeric password to open the door.
    // password_taken checks if the 4 digits have been input by the user using the bluetooth application on their phones.
    bool password_taken = false;
    
    // We have two audio files, on for warning when wrong password in entered and one for SOS alarm when all tries have been exhausted.
    FILE *wave_warning;  
    FILE *wave_alarm;
    
    // intializing the servo
    myservo =0;
    
    // clearing the LCD screen in  the beginning
    uLCD.cls();
    
    // Checks ToF distance sensor error status
    int status;
    // distance from the car
    uint32_t distance;
    // I2C device
    DevI2C *device_i2c = new DevI2C(VL53L0_I2C_SDA, VL53L0_I2C_SCL);
    board = XNucleo53L0A1::instance(device_i2c, A2, D8, D2);
    
    shdn = 0; //must reset sensor for an mbed reset to work
    wait(0.1);
    shdn = 1;
    wait(0.1);
    
    /* init the 53L0A1 board with default values */
    status = board->init_board();
     
    // check status 
    while (status) {
        pc.printf("Failed to init board! \r\n");
        status = board->init_board();
    }
    
    //loop taking and printing distance
    while (1) {
       
        status = board->sensor_centre->get_distance(&distance); // get distance 
        if (status == VL53L0X_ERROR_NONE && flag == false) {    // if there is no error and car is not present check for distance
            pc.printf("D=%ld mm\r\n", distance);
            if (distance <=300 && distance >0)                  // 300 mm is the distance threshold to detect car
            {
                uLCD.text_width(3); //font size
                uLCD.text_height(3);//font size
                uLCD.printf("ENTER PASSWORD!!");                // prompts user to enter password
                flag = true;                                    // turn flag true if car detected
            }
        }
        
        string password_og = "1243";                            // PASSWORD set to 1243
        string password = "";                                   // initialize string the take user input
        char bnum=0;                                
        char bhit =0;
        if (flag == true)                                       // if car present
        {
             password = "";                                     // intialize blank passwork
            while (warn_count <=3)
            {
                // blutooth check password
                // check bluetooth chars
                // bluetooth intialization
                bnum = 0;
                bhit = 0;
                password = "";
                
                int loopcount = 0;                              // to take 4 characters from the bluetooth app
                while(1){                                       // polling for characters
                    while(loopcount < 4)                        // for 4 characters
                    {
                if (blue.getc()=='!') {
                if (blue.getc()=='B') { //button data
                bnum = blue.getc();     //button number
                bhit = blue.getc();
                if (blue.getc()==char(~('!' + 'B' + bnum + bhit)))  // checksum
                    {
                        if(count == 0)
                        {
                            
                            password.push_back(bnum);   // push each characted entered by the user from their phone
                            count++;
                            loopcount++;
                        }
                        else
                        {
                            count = 0; // skips extra character from being read
                        }
                    }
                }
                }
                }
                password_taken = true; // make true when all 4 digits of the password have been entered
                break; // while loop
                } // while loop

                // checked password when taken
                    if(password_taken){
                if(password_og == password)     // if correct password entered
                {
                    uLCD.cls();        // clear LCD
                    uLCD.text_width(3);// font size
                    uLCD.text_height(3);
                    uLCD.color(GREEN); // set color to green
                    uLCD.printf("GO!");// print to GO!
                    redpin = 0;        // red led off
                    greenpin = 1;      // turn on green led
                    myservo = 1;       // open door
                    wait(15);          // give user 15 seconds to enter the garage
                    myservo =0;        // close door
                    greenpin=0;        // turn off green led
                    break;
                }
                 else                  // when wrong password entered
                  { 
                     redpin = 1;       // turn on red led
                     
                     if (alarm_flag == false)   // if user has trials left
                 {
                    uLCD.cls();                 // clear lcd
                    uLCD.text_width(3);//font size
                    uLCD.text_height(3);
                    uLCD.color(RED);   //set color to red
                    uLCD.printf("TRY AGAIN!");
                    // play the warning audio file
                    wave_warning = fopen("/sd/warning.wav","r");
                    waver.play(wave_warning);
                    fclose(wave_warning);
                    wait(3);
                    warn_count++;  // count the number if trials 
                     if (warn_count == 3) // if user is out of trial counts
                    {
                        alarm_flag = true;  // alram should go off
                    }
                    }
                    else                    // play the alarm when user is out of trials
                    {
                        uLCD.cls();     // clear screen    
                        uLCD.text_width(3);// set font size
                        uLCD.text_height(3);
                        uLCD.color(RED); // set color to red
                        uLCD.printf("ACCESS DENIED!");
                        wave_alarm = fopen("/sd/alarm.wav","r");
                        waver.play(wave_alarm);
                        fclose(wave_alarm);
                        wait(3);
                        alarm_flag=false;   // reset flag for alarm
                        redpin =0;          // switch off red led
                        break;
                        
                    }
            
                }
               
            }
            }
        
              flag = false;                 // reset flag for car present
              warn_count =0;                // reset warn counts
              uLCD.cls();                   // clear LCD
        }
    }
}

