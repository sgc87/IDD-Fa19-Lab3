# Data Logger (and using cool sensors!)

*A lab report by John Q. Student.*

## Prelab

I've thought of using a photoresistor to actuate a window open or closed depending on the brightness outside. 

![Window Photo Actuator](https://github.com/sgc87/IDD-Fa19-Lab3/blob/master/prelab.jpg)

## In The Report

Include your responses to the bold questions on your own fork of [this lab report template](https://github.com/FAR-Lab/IDD-Fa18-Lab2). Include snippets of code that explain what you did. Deliverables are due next Tuesday. Post your lab reports as README.md pages on your GitHub, and post a link to that on your main class hub page.

For this lab, we will be experimenting with a variety of sensors, sending the data to the Arduino serial monitor, writing data to the EEPROM of the Arduino, and then playing the data back.

## Part A.  Writing to the Serial Monitor
 
**a. Based on the readings from the serial monitor, what is the range of the analog values being read?**

The analog values range from 0 to 1023. 
 
**b. How many bits of resolution does the analog to digital converter (ADC) on the Arduino have?**

The ADC on the Arduino has a resolution of 10 bits. 

## Part B. RGB LED

**How might you use this with only the parts in your kit? Show us your solution.**

[Video Demo of RGB Led working](https://www.youtube.com/watch?v=CB5esAJPG8g&feature=share&fbclid=IwAR2kkAF1fJKoJnB0bRrXvGVVZKFgxHkr7HeEH4n8rtq-jdEAeoe0IaK2QFY)

You connect Pin1, Pin2, Pin3, and Pin4 on the RGB LED to Pin9, Pin10, VDD, and Pin11 on the Arduino repectively. 
Place resistors for Pins 1, 2, and 4. 

## Part C. Voltage Varying Sensors 
 
### 1. FSR, Flex Sensor, Photo cell, Softpot

**a. What voltage values do you see from your force sensor?**

The Analog output shows values from 0 to 1006 and my resistor was measured to be 26.8k Ohms

0 corresponds to 0V, and 1023 corresponds to 5V. Therefore, the ratio of maximum output and total possible output values multiplied by 5V is the voltage range. 

1006/1024 * 5V = 4.912V

Therfore the Voltage range we see from the FSR is from 0V to 4.912V

**b. What kind of relationship does the voltage have as a function of the force applied? (e.g., linear?)**

The relationship between voltagge and force applied looks to be logarithmic based on this graph from the datasheet. 

![Here is the Graph](https://github.com/sgc87/IDD-Fa19-Lab3/blob/master/im1.png)

**c. Can you change the LED fading code values so that you get the full range of output voltages from the LED when using your FSR?**

To map the values of the FSR to the RGB LED, one way is to have values 0-255 represent RED only output from off to fully on, 256-511 for GREEN, 512-767 BLUE, and 768-1006 represent RED, GREEN, and BLUE all on to create white light from off to fully one. 

To control just one LED, one could just divide the output from the FSR by 4 and round the number and/or mask the data type as an int. 
Masking something as an int automatically cuts off the decimals. 

The following code snippet in an example of how this value would be calculated. 
```
analogWrite(ledpin, int(FSR_Value/4))
```

**d. What resistance do you need to have in series to get a reasonable range of voltages from each sensor?**

Photoresistor: 
I used a potentiometer as a pull-down resistor with the photoresistor getting supply power. 

I tried different values using a potentiometer to test the value ranges and then tested the resistance value on the potentiometer using a multimter. A 10k potentiometer is a good tool for this, because the datasheet recommends to use a resistance value inbetween 5k and 10k. 

For a well lit room at night, I got the greatest range when the potentiometer was set to 6.1k Ohms. 
The values ranged from 540 to 822. 

Softpot: 
I also used a potentiometer as a pull-down resistor for this one as well. The wiring was the same as the photoresistor except that it had a ground pin. 

Although the website recommends using a 10k Ohm resistor as the pull down, I discovered that setting the resitance to 1.45 Ohms created the analog values with the greated amount of range, ranging from 85 to 1023. 

The recommended 10k Ohms of resistance results in analog values that start at around 390 and go up to the maximum 1023. This is a much narrower range of values. 

**e. What kind of relationship does the resistance have as a function of stimulus? (e.g., linear?)**

Photoresistor:
The relationship is deacreasingly logrithmic as shown by the following graph. 
![Photorisistor Graph](https://github.com/sgc87/IDD-Fa19-Lab3/blob/master/sensor_photoresistor_vt935g_slope.png)
The resistivity goes down as more light is received. 

Softpot: 
This also has a logrithmic relationship. 

### 2. Accelerometer
 
**a. Include your accelerometer read-out code in your write-up.**

Here is the beginning of the output
```
LIS3DH found!
Range = 4G
X:  432  	Y:  1680  	Z:  12000		X: 0.63 	Y: 1.69 	Z: 11.95 m/s^2 

X:  352  	Y:  1072  	Z:  7936		X: 0.50 	Y: 1.32 	Z: 9.66 m/s^2 

X:  432  	Y:  1040  	Z:  7936		X: 0.50 	Y: 1.32 	Z: 9.62 m/s^2 

X:  416  	Y:  1056  	Z:  7904		X: 0.54 	Y: 1.34 	Z: 9.46 m/s^2 

X:  384  	Y:  1088  	Z:  7920		X: 0.44 	Y: 1.30 	Z: 9.48 m/s^2 

X:  448  	Y:  1056  	Z:  7824		X: 0.46 	Y: 1.26 	Z: 9.46 m/s^2 

X:  416  	Y:  1072  	Z:  7856		X: 0.44 	Y: 1.30 	Z: 9.41 m/s^2 
```

[Video of Accelerometer Working](https://youtu.be/xF9uoT72Bts)

This is my code

```
// include the library code:
#include <LiquidCrystal.h>

#include <Wire.h>
#include <SPI.h>
#include <Adafruit_LIS3DH.h>
#include <Adafruit_Sensor.h>

// Used for software SPI
#define LIS3DH_CLK 13
#define LIS3DH_MISO 12
#define LIS3DH_MOSI 11
// Used for hardware & software SPI
#define LIS3DH_CS 10

// software SPI
//Adafruit_LIS3DH lis = Adafruit_LIS3DH(LIS3DH_CS, LIS3DH_MOSI, LIS3DH_MISO, LIS3DH_CLK);
// hardware SPI
//Adafruit_LIS3DH lis = Adafruit_LIS3DH(LIS3DH_CS);
// I2C
Adafruit_LIS3DH lis = Adafruit_LIS3DH();

// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup(void) {
#ifndef ESP8266
  while (!Serial);     // will pause Zero, Leonardo, etc until serial console opens
#endif

  Serial.begin(9600);
  Serial.println("LIS3DH test!");
  lcd.begin(16, 2);
  
  if (! lis.begin(0x18)) {   // change this to 0x19 for alternative i2c address
    Serial.println("Couldnt start");
    while (1);
  }
  Serial.println("LIS3DH found!");
  
  lis.setRange(LIS3DH_RANGE_4_G);   // 2, 4, 8 or 16 G!
  
  Serial.print("Range = "); Serial.print(2 << lis.getRange());  
  Serial.println("G");
}

void loop() {
  lis.read();      // get X Y and Z data at once
  // Then print out the raw data
  Serial.print("X:  "); Serial.print(lis.x); 
  Serial.print("  \tY:  "); Serial.print(lis.y); 
  Serial.print("  \tZ:  "); Serial.print(lis.z); 

  /* Or....get a new sensor event, normalized */ 
  sensors_event_t event; 
  lis.getEvent(&event);
  
  /* Display the results (acceleration is measured in m/s^2) */
  Serial.print("\t\tX: "); Serial.print(event.acceleration.x);
  Serial.print(" \tY: "); Serial.print(event.acceleration.y); 
  Serial.print(" \tZ: "); Serial.print(event.acceleration.z); 
  Serial.println(" m/s^2 ");

  lcd.setCursor(0, 0);
  lcd.print("x=" + String(event.acceleration.x));
  lcd.setCursor(9, 0);
  lcd.print("y=" + String(event.acceleration.y));
  lcd.setCursor(0, 2);
  lcd.print("z=" + String(event.acceleration.z));
  lcd.setCursor(7, 2);
  lcd.print("m/ss");

  Serial.println();
 
  delay(200); 
}
```

## Part D. Graphic Display

**Take a picture of your screen working insert it here!**

[Link to working video](https://youtu.be/SGC4oZQQtdM)

![OLED](https://github.com/sgc87/IDD-Fa19-Lab3/blob/master/oled.jpg)

Here is the code for it
```
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 32 // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET     4 // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int analog_in = A0;
int oled = 0;

void setup() {
  Serial.begin(9600);

  // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3C for 128x32
    Serial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
}

void loop() {
  oled = analogRead(analog_in);
  display.setTextSize(2);
  display.setTextColor(WHITE);
  display.setCursor(0,0);
  display.print(F("Force Ind:"));
  display.setCursor(0, 18);
  display.print(oled);
  display.display();
  delay(100);
  display.clearDisplay();
}
```

## Part D. Logging values to the EEPROM and reading them back
 
### 1. Reading and writing values to the Arduino EEPROM

**a. Does it matter what actions are assigned to which state? Why?**

Yes, it matters, because doing something out of order can cause a system to not work at all. For example, you wouldn't want to output data before getting any inputs. 

**b. Why is the code here all in the setup() functions and not in the loop() functions?**

This is because, we don't want the different states to run non-stop simutaneously. That would not be a state function. Instead you want the functions to be called only when you are in the specific state that runs that function. 

**c. How many byte-sized data samples can you store on the Atmega328?**

Atmega allows you to store 1024 bit sized data. 

**d. How would you get analog data from the Arduino analog pins to be byte-sized? How about analog data from the I2C devices?**

Arduino analog pins have 10 bits of data, so it can take on 8 bits of data without any modification. 

I2C sends packets in 8-bits, so no adjustment needed here as well. 

**e. Alternately, how would we store the data if it were bigger than a byte? (hint: take a look at the [EEPROMPut](https://www.arduino.cc/en/Reference/EEPROMPut) example)**

If the data is bigger than a byte, it would be send in sequential packets to send the information over and then concatentated in memory. 

**Upload your modified code that takes in analog values from your sensors and prints them back out to the Arduino Serial Monitor.**

Here's my state machien code. 
```
#include <EEPROM.h>
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 32 // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET     4 // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int analog_in = A0;
int sensor = 0;

int pushbutton = 0;
int weightsensor;
int button;

byte eepromAddr;
int add = 0;
int addr_len = 0;

int state = 1;
int push;

int time_count = 0;

int ee_num;

void setup() {
  Serial.begin(9600);

  // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3C for 128x32
    Serial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
  pinMode(analog_in, INPUT);
  pinMode(pushbutton, INPUT);
  while (!Serial) {
    ;
  }
}

void loop() {
  display.setTextSize(2);
  display.setTextColor(WHITE);
  display.clearDisplay();
  weightsensor = analogRead(pushbutton);
  button = digitalRead(pushbutton);
  if (state == 1) {
    if (weightsensor > 200) {
      state = 2;
    }
    display.clearDisplay();
    display.setCursor(0,0);
    display.print(F("Park and  pay later"));
    display.display();
    delay(100);
  }
  if (state == 2) {
    if (weightsensor > 200) {
      EEPROM.write(add, time_count);
      add++;
      if (add == EEPROM.length()) {
        add = 0;
      }
      display.clearDisplay();
      display.setCursor(0,0);
      display.print(F("Time:"));
      display.setCursor(0, 18);
      display.print(time_count);
      display.display();
      delay(1000);
      time_count++;
    }
    if (weightsensor < 200) {
      state = 3;
    }
  }
  if (state == 3) {
    display.clearDisplay();
    display.setCursor(0,0);
    display.print(F("Amount Due$"));

    display.print((time_count - 1));
    display.display();
    delay(100);
    if (button == 0) {
      state = 1;
      time_count = 0;
    }
    eepromAddr = EEPROM.read(addr_len);
    Serial.print(addr_len);
    Serial.print("\t");
    Serial.print(eepromAddr, DEC);
    addr_len++;
    if (addr_len == EEPROM.length()) {
      addr_len = 0;
      delay(500);
    }
  }
  display.clearDisplay();
}
  
```

### 2. Design your logger
 
**a. Insert here a copy of your final state diagram.**

![State Diagram](https://github.com/sgc87/IDD-Fa19-Lab3/blob/master/im2.PNG)

### 3. Create your data logger!
 
**a. Record and upload a short demo video of your logger in action.**

[Video of State Machine!](https://www.youtube.com/watch?v=DX7BOJ-83vs&feature=share&fbclid=IwAR02bAwMiAQa9ZHPidH8ulxZSJCGgI-Jlu8B2DkU4JyJ1rqLTDrVbA-T9bc)
