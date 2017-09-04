<head>
<link rel="stylesheet" href="myStyles.css">
</head>

<div class="top-navbar">
  <a href="index.html">Home</a>
  <a href="about.html" class="drop-button current">About</a>
    <div class="drop-menu">
      <a href="about.html">Logistics</a>
      <a href="about.html">Team Contract</a>
      <a href="about.html">Meeting Minutes</a>
      <a href="about.html">Members</a>
    </div>
  <a href="assignments.html">Assignments</a>
  <a href="tutorials.html">Tutorials</a>
  <a href="contact.html">Contact</a>
</div>

# Purpose
* Get familiar with the Arduino Uno and IDE
* Take initial steps in building and controlling our robot

# Lab Materials
* Parallax Continuous Rotation Servo
* Arduino Uno
* LED
* 3306F-103 Potentiometer
* 330 Ohm Resistors
* Line sensor QRE1113

# 1. Blinking An Internal LED
The first mini project is to blink the Internal LED on the Arduino board every second. A built-in LED is already connected to the Arduino board and it is wired to Pin 13. The following code, one of the Arduino IDE example programs, sets up Pin 13 as digital output in the setup() function, and it then repeatedly turns Pin 13 HIGH, waits for one second, turns Pin 13 LOW, waits for one second, creating the blinking effect.
![Blinking Arduino's Internal LED](./images/code_blink_internal_LED.png)

# 2. Blinking an External LED
The second mini project is to blink an external LED, which is wired to the digital output Pin 3. There is 330 ohms resistor connected in series with the LED to limit current flow, otherwises, the LED would burn out. The code explanation is the same as the previous mini project except pin 3 is used instead of pin 13.
![Blinking Arduino's External LED](./images/code_blink_external_LED.png)
![Blink External LED Hardware Setup](./images/setup_blink_external_LED.png)

# 3. Reading Value of Potentiometer via Serial Port
In the third mini project, we built a voltage divider and measured the voltage across the potentiometer using analog pin 0. The Arduino has a 10 bit ADC, so its resolution is 2^10 = 1024. When using analog read from Pin 0, it returns a value from 0 to 1023. To convert the input value into voltage, voltage drop across potentiometer = reading value*5/1024. The circuit setup can be viewed below. 
![Reading Value of Pot via Serial Port](./images/setup_read_pot_serial.png)
![Value of Pot via Serial Port Code](./images/code_read_pot_serial.png)

# 4. Map the Value of the Potentiometer to the LED
In this part we used the setup from the previous project to read the voltage drop across a potentiometer, and mapped that analog input to a pulse-width modulator (PWM) that will drive a separate external LED. The point is to have the brightness of the LED be proportional to the voltage drop across the potentiometer, which is itself proportional to the value of the potentiometer itself. 
The voltage divider is set up so that a 330Ohm resistor is placed between 5V and the pin A0, and the potentiometer is connecting A0 to ground. We use the analogRead() function on the A0 pin to get the value of the voltage across the potentiometer, called ‘val’. We connect an LED and a resistor to a digital pin with PWM capability, and drive it using the analogWrite() function. The analogRead() function has 1024 possible output values, but the analogWrite() function can only take inputs between 0 and 255, so we divide the value of ‘val’ by 4 and write that to analogWrite(). 
![Map Value of Pot to LED](./images/code_pot_to_led.png)
![LED Reading From Pot](./video/pot_to_led.png)

# 5. Map the Value of the Potentiometer to the Servo

# 6. Assemble Our Robot
After completing the first five projects, we began assembling our robot. Using the mounts provided for us, we attached two servos and the Arduino Uno to a small chassis, and placed a small breadboard temporarily underneath the Arduino to facilitate wiring. We wired the power and ground pins of the servos through the breadboard and to an external power supply providing 5V output, and connected the data lines of the two servos to pins 3 and 5 of the Arduino (for left and right servos, respectively). Additionally, we mounted two QRE1113 line sensors to the front of the robot and wired their data lines to the Arduino’s A1 and A2 analog inputs. We provided power to the line sensors by wiring them through the breadboard to the Arduino’s 5V and GND pins.
![Preliminary Robot](./images/lab1_robot1.png)
![Preliminary Robot](./images/lab1_robot2.png)

# 7. Driving Our Robot Autonomously

# Extra FUN
### F is for friends who do stuff together, U is for you and me, N is for anywhere and anytime at all, down here in Phillips 427.
Testing the servo: the servo turns clockwise when set to 180, stops turning when set to 90, and turns clockwise when set to 0
![Test servo rotation][./images/lab1_code_test_servo.png]
Soldering: soldered pins to the line sensor QRE1113
![Soldered pins on line sensor][./images/lab1_line_sensor_solder.png]