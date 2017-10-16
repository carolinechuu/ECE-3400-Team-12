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
<br>

# Purpose

# Important Components
* [DE0-Nano FPGA board](http://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=165&No=593&PartNo=4)
* 8-bit DAC
* Speaker and audio socket
* VGA switch, screen, cable, and connector

# Graphics Team: Caroline, Xitang, Felipe

# Acoustic Team: Christina, Ian, Pei-Yi

## Square Wave Generation
The simplest wave that can be generated with the FPGA is the square wave. On the FPGA, this involves keeping a counter that will reset whenever we toggle the output. Toggling takes place at a rate of twice the frequency we want to play. If we want to play a 440Hz tone, for example, we would have to toggle our output at a rate of 880Hz, once for the rising edge and once for the falling edge. The counter keeps track of how many clock cycles must pass before we toggle.
<!--Insert code snippet and image of generated wave-->

## Triangular and Sawtooth Wave Generation
Triangular and sawtooth waves are a little more involved that square waves. For a sawtooth wave, our counter keeps track of how many clock cycles in just one period. While the counter is still "ticking", we increment the value outputted through the DAC. When the counter reaches 0, we reset the counter and also reset the value outputted through the DAC to 0, which begins the next period.
<br>
The triangular wave is a little more involved. We do something very similar to the sawtooth wave, but we need to increment and decrement the output, depending on where we are on the wave. A register keeps track of whether we are ascending/descending, and the counter is back to twice the frequency that we want to play, once to make our output descend, and once to make it ascend.

<table>
	<tr>
		<td>Sawtooth Wave</td>
		<td>Triangular Wave</td>
	</tr>
	<tr>
		<td><img src="sawtooth.jpg"></td>
		<td><img src="triangle.jpg"></td>
	</tr>
</table>

Please note that, in the diagrams, our waves have extremely high frequencies that aren't audible. We did change these frequencies by increasing our counter variable, so that the waves can be heard when played on the speaker.

## Sine Wave Generation
Sine waves can't be generated using the methods described above because there is no "nice behavior" where we can simply increment a register and output that register's value. Therefore, we must use a sine table. This involves saving the values of a sine wave in ROM, which provides a lookup for the value we should be outputting based on where we are in the wave. We save 256 points in our ROM table, so one period contains 256 points. In this case, our counter must keep track of how many clock cycles pass between each of the 256 points.
<img align="center" src="sine.jpg">

## 3 Note Tune
 To generate a 3 note tune, we needed to implement a finite state machine to switch between notes every second. We created parameters corresponding to the number of clock cycles before switching to the next value in the ROM table such that we achieve our desired frequency (this is the maximum value of counter). The note register encodes the current note being played while the note_length register keeps track of the number of clock cycles to play the current note.
	In the state machine, we hold one note for one second. After the one second counter expires, we increment note by one to move to the next note. Within the sine generation logic, we implement a multiplexer to set the period of the generated sine wave and thus the note being played through the speakers. The result is as desired; the FPGA outputs a periodic tone of three frequencies to the 8-bit DAC which plays through the speakers. The tune is: Pause, A, C#, E each for one second on a loop.
```
localparam CLKDIVIDERA = ONE_SEC/(256*440);
localparam CLKDIVIDERCsh = ONE_SEC/(256*554);
localparam CLKDIVIDERE = ONE_SEC/(256*660);

...

reg [15:0] counter;
reg [25:0] note_length;
reg [1:0] note;

...

if(note_length == 0) begin
	note_length <= ONE_SEC;
	note <= note + 1;
end

...

case(note)
	2'b00: counter    <= ONE_SEC;
	2'b01: counter    <= CLKDIVIDERA - 1;
	2'b10: counter    <= CLKDIVIDERCsh - 1;
	2'b11: counter    <= CLKDIVIDERE - 1;	
endcase

```
