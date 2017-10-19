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

# Prelab
Radio Team

If you are sending a 2-dimensional (3x3, for example) array of chars, what is the size of the array in bytes? 
9 bytes. 1 char = 1 byte

Compare this to the maximum payload size of the radio. How many packets are required to send the 3x3 char array?
1 packet, since we can send 32 bytes as maximum payload size.

Now assume that each element in the array has a maximum value of 3. How many bytes can you compress this array into, now that you know this piece of information? 
Maximum value of 3 means we only use 2 bits for each element. 2 * 9 = 18 bits = ceil(18 / 8) = 3 bytes. 3 bytes

How many packets are now required to send the array?
1 packet
