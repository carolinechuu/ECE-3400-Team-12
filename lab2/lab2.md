<head>
<link rel="stylesheet" href="../myStyles.css">
</head>

<div class="top-navbar">
  <a href="../index.html">Home</a>
  <a href="../about.html">About</a>
  <a href="../assignments.html" class="current">Assignments</a>
  <a href="../tutorials.html">Tutorials</a>
  <a href="../contact.html">Contact</a>
</div>

# Pre-Lab
The goal of this lab is to add two sensors to our robot. The first is a microphone with which we must be able to detect a 660Hz whistle, and the second is an IR sensors with which we must be able to detect an infrared light blinking at 7kHz. To process the analog data received by the two sensors, we must make use of our on-chip Analog-to-Digital converters. The ATmega328 has an internal ADC with 10 bits of resolution, driven by an ADC clock we can control through a prescaler. The ADC takes 12 ADC clock cycles to initialize, then is capable of completing a single conversion in 13 ADC clock cycles. In a normal conversion, the system does a sample-and-hold at 1.5 ADC clock cycles into the conversion, holding the analog value found at that time. When conversion is complete, the result is written to the ADC Data Registers (ADCL and ADCH), and the ADC Interrupt Flag is set.

The ADC can operate in one of two modes: single conversion or Free Running mode. In Free Running mode, the ADC auto-triggers, meaning it starts a new conversion as soon as the ongoing one is finished. The ADC is constantly sampling and updating the ADC Data Register, and although the ADC Interrupt Flag is set each time, the conversions continue regardless of whether it is getting cleared. Each conversion still takes 13 clock cycles, but an extra half cycle is needed between conversions to reset the prescaler, requiring 13.5 cycles per conversion. 

Successive approximation requires an input clock frequency of 50kHz to 200kHz for maximum resolution, which means data is being sampled at somewhere between around 4kHz to 15kHz in Free Running mode. In other words, each conversion takes between 70us and 270us. We can increase the clock frequency if we need fewer than 10 bits of resolution, but that probably won't be the case. The Arduino's "analogRead()" function is said to read at 100us per conversion, so the ADC clock frequency must be greater than 135kHz to be sampling faster.

Whether referring to the acoustic data from the microphone or IR light data from the detector, it is important that we sample at a rate at least twice as fast as the lowest frequency we're detecting in order to correctly identify all frequencies. The microphone is being used to detect a frequency of 660Hz, and any background noise, most likely caused by people speaking, would be mostly less than 4kHz, meaning a sampling frequency of 8kHz would suffice, so either analogRead() function would sample quickly enough. The IR treasure, however, blinks at 7kHz, meaning our ADC should preferably be sampling at least at 14kHz. This is nearing our maximum sampling rate of 15kHz, and is well above the sampling frequency of 10kHz that analogRead() reaches

In order to improve the quality of the input signals, we could try to make use of amplifiers and filters. We would need to analyze the FFT to determine whether we need a low-pass or high-pass filter, but I would guess that a low-pass filter will be more useful in both cases. For detecting a 660Hz acoustic signal, for example, a cutoff frequency of 1kHz would be conservative yet helpful, while for the 7kHz IR signal a cutoff frequency somewhere in the range of 9kHz could prove useful. We can implement simple passive filters using RLC circuits, or we can use an operational amplifier to implement an active low-pass filter, by connecting a resistor and capacitor in parallel in feedback. We can manipulate the values of the resistor and capacitor to set a cutoff frequency, and because we use an OpAmp the signal will also be amplified. We can also use an operational amplifier simply to amplify the signal, by tying the input signal to the negative end of the opamp, placing the positive end at a constant DC voltage, and putting a resistor in feedback. 

# Arduino Analog-to-Digital Converter (ADC)
[Library function analogRead()](https://www.arduino.cc/en/Reference/AnalogRead)
Function analogRead() converts an analog voltage value (0-5V) to a 10-bit digital value (0-1023).

# All The Fourier Stuff
**Fourier Transforms**<br>
Here is a great PhET simulation for the basic concepts for FFT. The idea is that we take a time-dependent wave and express it as a sum of sinusoids. We only need to specify each sinusoid's coefficient and phase to express our data.
<div style="position: relative; width: 300px; height: 197px;"><a href="https://phet.colorado.edu/sims/fourier/fourier_en.jnlp" style="text-decoration: none;"><img src="https://phet.colorado.edu/sims/fourier/fourier-600.png" alt="Fourier: Making Waves" style="border: none;" width="300" height="197"/><div style="position: absolute; width: 200px; height: 80px; left: 50px; top: 58px; background-color: #FFF; opacity: 0.6; filter: alpha(opacity = 60);"></div><table style="position: absolute; width: 200px; height: 80px; left: 50px; top: 58px;"><tr><td style="text-align: center; color: #000; font-size: 24px; font-family: Arial,sans-serif;">Click to Run</td></tr></table></a></div>

[Fast Fourier Transforms]
[Open Music Labs Arduino Library]
