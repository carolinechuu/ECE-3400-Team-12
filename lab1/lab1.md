<head>
<link rel="stylesheet" href="../myStyles.css ">
</head>

<div class="top-navbar">
  <a href="~/index.html">Home</a>
  <a href="~/about.html" class="drop-button current">About</a>
    <div class="drop-menu">
      <a href="~/about.html">Logistics</a>
      <a href="~/about.html">Team Contract</a>
      <a href="~/about.html">Meeting Minutes</a>
      <a href="~/about.html">Members</a>
    </div>
  <a href="~/assignments.html">Assignments</a>
  <a href="~/tutorials.html">Tutorials</a>
  <a href="~/contact.html">Contact</a>
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
```
// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin 13, the onboard LED, as an output.
  pinMode(13, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(13, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);              // wait for a second
  digitalWrite(13, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);              // wait for a second
}	
```

# 2. Blinking an External LED
The second mini project is to blink an external LED, which is wired to the digital output Pin 3. There is 330 ohms resistor connected in series with the LED to limit current flow, otherwises, the LED would burn out. The code explanation is the same as the previous mini project except pin 3 is used instead of pin 13.
```
int ledPin;

void setup() {
	ledPin = 3;
	pinMode(ledPin, OUTPUT);
}

void loop() {
	digitalWrite(ledPin, HIGH);	// turn the LED on
	delay(1000);				// wait for a second
	digitalWrite(ledPin, LOW);	// turn the LED off
	delay(1000);				// wait for a second
}
```
![Blink External LED Hardware Setup](blink_external_LED.jpg)

# 3. Reading Value of Potentiometer via Serial Port
In the third mini project, we built a voltage divider and measured the voltage across the potentiometer using analog pin 0. The Arduino has a 10 bit ADC, so its resolution is 2^10 = 1024. When using analog read from Pin 0, it returns a value from 0 to 1023. To convert the input value into voltage, voltage drop across potentiometer = reading value*5/1024. The circuit setup can be viewed below.
![Wiring Setup for Reading Value of Pot](setup_pot_serial_display.png)
![Reading Value of Pot via Serial Port](code_pot_serial_display.jpg)

# 4. Map the Value of the Potentiometer to the LED
In this part we used the setup from the previous project to read the voltage drop across a potentiometer, and mapped that analog input to a pulse-width modulator (PWM) that will drive a separate external LED. The point is to have the brightness of the LED be proportional to the voltage drop across the potentiometer, which is itself proportional to the value of the potentiometer itself.

The voltage divider is set up so that a 330Ohm resistor is placed between 5V and the pin A0, and the potentiometer is connecting A0 to ground. We use the analogRead() function on the A0 pin to get the value of the voltage across the potentiometer, called ‘val’. We connect an LED and a resistor to a digital pin with PWM capability, and drive it using the analogWrite() function. The analogRead() function has 1024 possible output values, but the analogWrite() function can only take inputs between 0 and 255, so we divide the value of ‘val’ by 4 and write that to analogWrite().  This means the duty cycle of the digital pin driving the LED is equal to (‘val’/4)/255 = (‘val’/1020), where ‘val’ varies between 0 and 1020.
```
int potInput;
int ledPin;

void setup() {
	potInput = A0;
	ledPin = 3;
	pinMode(ledPin, OUTPUT);
	pinMode(potInput, INPUT);
}

void loop() {
	int val = analogRead(potInput);
	analogWrite(ledPin, val/4);
	delay(100); // optional, updates LED 10 times a second
}
```
![Wiring LED to Pot](pot_to_led.jpg)
<!-- ![LED Reading From Pot](pot_to_led.mp4) -->

# 5. Map the Value of the Potentiometer to the Servo
Acquiring a reading from the potentiometer for this task was the same as the previous task, as can be seen from the code. This portion, however, also requires that we drive a servo. To drive the servo, we used the Arduino Servo library. Using this library, we created a Servo object called myservo and used the attach() function to send the output to pin 3 on the Arduino in our setup.

In the loop, we wrote a value to myservo using the Servo write() function which takes values between 0 and 180 as input and outputs a PWM signal to control the servo. For our continuous rotation servos 0 to 89 rotates the servo counter-clockwise, 90 stops the servo, and 91 to 180 rotates the servo clockwise. We mapped the potentiometer reading (0 to 1023) to the servo (0 to 180) by dividing the potentiometer reading by 5.7 (approximately 1023/180). 

Below are oscilloscope readings for the PWM outputs of the Arduino. PWM (Pulse Width Modulation) is a protocol used to control motors through, as the name states, modulating the width of pulses (square waves). For our servos, the pulses are typically between 1ms and 2ms in width and 5V in amplitude. The Arduino Servo library creates these pulses by mapping the function input of 0-180 to a pulse width from 1ms to 2ms.
```
int potInput;
Servo myservo;

void setup() {
	potInput = A0;
	myservo.attach(3);
	pinMode(potInput, INPUT);
}

void loop() {
	int val = analogRead(potInput);
	myservo.write(val/5.7);
	delay(100); //optional, updates servo value 10 times a second
}
```
![Reading from oscilloscope](oscilloscope1.png)
![Reading from oscilloscope](oscilloscope2.png)
![Reading from oscilloscope](oscilloscope3.png)

# 6. Assemble Our Robot
After completing the first five projects, we began assembling our robot. Using the mounts provided for us, we attached two servos and the Arduino Uno to a small chassis, and placed a small breadboard temporarily underneath the Arduino to facilitate wiring. We wired the power and ground pins of the servos through the breadboard and to an external power supply providing 5V output, and connected the data lines of the two servos to pins 3 and 5 of the Arduino (for left and right servos, respectively). Additionally, we mounted two QRE1113 line sensors to the front of the robot and wired their data lines to the Arduino’s A1 and A2 analog inputs. We provided power to the line sensors by wiring them through the breadboard to the Arduino’s 5V and GND pins.
![Preliminary Robot](robot1.jpg)
![Preliminary Robot](robot2.jpg)

# 7. Driving Our Robot Autonomously
To drive the robot, we have to set up two servos, which are connected to Pin 3 and Pin 5, respectively. We used these two pins, because they are the first two pins that can do PWM. We created three functions to drive the robot: walkForward(), walkLeft() and walkRight(). 

To drive the robot forward, two servos need to move in the same directions. Because the two servos are mirrored of each other, the values we are writing to them are opposite of each other: 180 to left and 0 to right. To drive the robot to the left, we stop the left servo by writing 90 to it and continue moving right servo forward by writing 0 to it. Similarly, to drive the robot to the right, we stop the right servo by writing 90 to it and continue moving left servo forward by writing 180 to it.
```
#include <Servo.h>
int lineMid1, lineMid2; //Line Sensor Values Variables
Servo left, right;
int toleranceForward = 100;
int blackDetect = 850;

//Function for robot to walk
void walkForward(){left.write(180);right.write(0);}
void walkLeft(){
  left.write(90); //Stop left
  right.write(0); //Move left to turn left
}
void walkRight(){
  left.write(180); //Move left to turn right
  right.write(90); //Stop right
}
```
We also have two line sensor values readings, which are stored into variables lineMid1 and lineMid2. The line sensors are used to keep the robot to move along the line. The line sensor value reading is ranged from 0 to 1023. If it is in the high end 900+, it means it is on top of the black line. If it is in the low end, less than 700 or 400 (depending how close it is to the floor),  it means it is on top of the white line. We attached two line sensors to the front of our robot: lineMid1 is the left one and lineMid2 is the right one. If the difference between the two line sensors value is less than a tolerance value, say 100, it means the robot is on top of the black line and we just move forward. Else, if the left sensor has higher value than the right sensor, it means the robot is tilted to the right white space and we have to turn left, so we do the walkLeft() function. Similarly, if the right sensor has higher value than the left sensor, it means the robot is tilted to the left white space and we have to turn right, so we do the walkRight() function.
```
//Main Function
void setup() {
  Serial.begin(9600);  
  left.attach(3); right.attach(5); //Setup Servo
}

void loop() {
   lineMid1 = analogRead(1);
   lineMid2 = analogRead(2);
   //Make sure it walks straight
   if (abs(lineMid1-lineMid2) < toleranceForward){
    walkForward(); 
   }
   else if (lineMid1 >= lineMid2){ // right side is white, turn left
    walkLeft();
   }
   else if (lineMid1 < lineMid2){
    walkRight();
   }

   Serial.print(lineMid1); //Debugging            
   Serial.print("\t"); 
   Serial.print(lineMid2);            
   Serial.println("\t");  
}
```

# Extra FUN
### F is for friends who do stuff together, U is for you and me, N is for anywhere and anytime at all, down here in Phillips 427.
*Testing the servo*: the servo turns clockwise when set to 180, stops turning when set to 90, and turns clockwise when set to 0
```
Servo myservo;

void setup() {
	myservo.attach(3);
}

void loop() {
	// Test servo
	myservo.write(180); //CCW
	delay(1000);
	myservo.write(90); //Stops servo
	delay(1000);
	myservo.write(0); //CW
	delay(1000);
}
```

*Soldering*: soldered pins to the line sensor QRE1113
![Soldered pins on line sensor][line_sensor_solder.jpg]
