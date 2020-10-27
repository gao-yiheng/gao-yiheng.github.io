---
layout:     post
title:      FPGA Serial II - Display Seven-Segment
subtitle:   Control 4-digit Seven-Segment with Both Software and Hardware Method
date:       2019-06-08
author:     yg
header-img: img/post-bg-5.jpg
catalog: true
tags:
    - How To's
    - FPGA
---


### OVERVIEW
This tutorial shows how to control 7-segment on Nexys3. Two method will be introduced: `software` and `hardware`. Software method will generate 7-segment control signals with `PicoBlaze` while hardware method will focus on controlling 7-segment with `Verilog`.


### STEP 1: Introduction of Seven-Segment

The Nexys3 board contains a four-digit `common anode` seven-segment LED display. Each of the four digits is composed of seven segments arranged in a **“figure 8”** pattern, with an LED embedded in each segment as the picture shows. 

![VB4XFJ.jpg](https://s2.ax1x.com/2019/06/08/VB4XFJ.jpg)

There are two types of LED 7-segment display: `Common Cathode (CC)` and `Common Anode (CA)`. In CA, all the **anode connections of the LED segments are joined together to logic “1”** as the picture shows. The individual segments are **illuminated by applying a ground, logic “0” or “LOW”** signal via a suitable current limiting resistor to the Cathode of the specific segment (a-g). More details for 7-segment display please refers to **REF [2]**. 

![VB4Lo4.jpg](https://s2.ax1x.com/2019/06/08/VB4Lo4.jpg)

To drive `CA` seven-segment, put “0” to I/O port to light LED. For example, if you want to display `1` as the picture shows, the output port for `abcdefg` is `1001111`. 

![VB4qwF.jpg](https://s2.ax1x.com/2019/06/08/VB4qwF.jpg)

The truth table is for displaying 1 to 9 with `CA` 7-segment.

![VB4jY9.jpg](https://s2.ax1x.com/2019/06/08/VB4jY9.jpg)



### STEP 2: Display 4-digit Number in Software

Nexys3 provides four-digit seven-segment, namely, 4 digits can display at the same time (see picture 1 in step 1). As we said before, each seven-segment needs **8-bit data to fully display**, which means `4x8=32-bit` are needed to display 4 seven-segment. We do not have so much I/O pins for seven-segment. To overcome this defect, Nexys3 **share the same data bus** (8 LEDs, from A to DP as previous table shows) for 4 digit seven-segment. Therefore, it’s not easy to display 4 digits at the same time. To make sharing bus work, Nexys3 provides **four triodes to enable each seven-segment**. To display **multi-digits** instead of only one digit, you will need to **enable each triode one by one** and **send corresponding data to CA to DP port**. But pay attention, **delay for short time after enable a triode** will need to guarantee triode works correctly. Because **data for displaying numbers has no regular pattern**, you cannot calculate them but can use `look-up table`. 

Now let’s do some fun thing with seven-segment! Suppose we want to **read 8-bit input from slide switches** and **split 8-bit input into two 4-bit numbers, add them** and **display numbers and add result on seven-segment** in hex. Following picture shows the flow chart in assembly way.

![VB4beU.jpg](https://s2.ax1x.com/2019/06/08/VB4beU.jpg)


### STEP 3: Display Four Digit Numbers in Verilog

Previous step discusses how to display seven-segment with software, this part will focus on displaying seven-segment in `Verilog`. We will use the same task to illustrate how to control seven-segment in Verilog code. Adder result will be output using **out_port[7:0]**, using `bcd_display module` **transferring 8-bit result into decimal and display on seven-segment** in Nexys3 as the picture shows.

![VB59OK.jpg](https://s2.ax1x.com/2019/06/08/VB59OK.jpg)

![VB5PeO.jpg](https://s2.ax1x.com/2019/06/08/VB5PeO.jpg)

The code in this step is [here](https://github.com/yg9120/Nexys3/tree/master/LightSevenSegment). Note: Code for Decode data into decimal comes from **REF [3]**, code for `BCD_Display module` based on [3]


### REF

[1] https://reference.digilentinc.com/reference/programmable-logic/nexys-3/reference-manual

[2] https://www.electronics-tutorials.ws/blog/7-segment-display-tutorial.html

[3] http://bits.usc.edu/ee209/labs.html

