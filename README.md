# Data Logger (and using cool sensors!)

*A lab report by John Q. Student.*

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

### 2. Accelerometer
 
**a. Include your accelerometer read-out code in your write-up.**


## Part D. Graphic Display

**Take a picture of your screen working insert it here!**

## Part D. Logging values to the EEPROM and reading them back
 
### 1. Reading and writing values to the Arduino EEPROM

**a. Does it matter what actions are assigned to which state? Why?**

**b. Why is the code here all in the setup() functions and not in the loop() functions?**

**c. How many byte-sized data samples can you store on the Atmega328?**

**d. How would you get analog data from the Arduino analog pins to be byte-sized? How about analog data from the I2C devices?**

**e. Alternately, how would we store the data if it were bigger than a byte? (hint: take a look at the [EEPROMPut](https://www.arduino.cc/en/Reference/EEPROMPut) example)**

**Upload your modified code that takes in analog values from your sensors and prints them back out to the Arduino Serial Monitor.**

### 2. Design your logger
 
**a. Insert here a copy of your final state diagram.**

### 3. Create your data logger!
 
**a. Record and upload a short demo video of your logger in action.**
