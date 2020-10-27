---
layout:     post
title:      Summary of Git Commands (Commonly Used) (Under Construction)
subtitle:   
date:       2020-10-21
author:     yg
header-img: img/post-bg-5.jpg
catalog: true
tags:
    - Knowledge Base
    - Tools
---


### OVERVIEW
This tutorial shows how to use PicoBlaze in FPGA (Nexys3 board). The first part will introduce how to get started with PicoBlaze and light 8 LEDs installed in Nexys3 FPGA board. Code for this part is [here](https://github.com/yg9120/Nexys3/tree/master/LightLEDS)


### STEP 1: Prepare Materials for Your Design

Two software in this tutorial were used to develop the FPGA design: `ISE` and `Adept`. **ISE** is an IDE tool that could simulates and synthesis your design and finally generates a bit file which could be downloaded into FPGA board (Nexys3) using **Adept**.

To begin with, design files related with PicoBlaze are needed. Go to the [PicoBlaze Page](https://www.xilinx.com/products/intellectual-property/picoblaze.html#design) to download the `KCPSM6 ...` zip file. 

![VBETkF.jpg](https://s2.ax1x.com/2019/06/08/VBETkF.jpg)

There are several **useful things** in the zip file for your design. `KCPSM6_User_Guide` includes **general design flow** with PicoBlaze **(p6-13)** and explanations of each instruction and so on. `kcpsm6.exe` is a compiler that translates the assembly file into machine code which is the format can be accessed by PicoBlaze microprocessor. The PicoBlaze **components and its connections** can be found in **user guide page 8**. 

This tutorial would select **Verilog** as Hardware Description Language and therefore, move into `Verilog folder` in zip file. In this folder, `kcpsm6.v` is the source code for PicoBlaze (also called `CDPSM6`) while `ROM_form.v` would be used with `kcpsm6.exe` to generate your memory space. `kcpsm6_design_template.v` is also helpful though it is only a template file, not a Verilog file that can be simulated or synthesis directly. 


### STEP 2: Write Your First Assembly Code with PicoBlaze

Write the following assembly code with any text editor (e.g. Notepad) and save as `xxx.psm` file. The code would light LEDs with the position in 7, 5, 2, 1, 0.

```
CONSTANT Acheck, 10100111’b
CONSTANT LED_PORT, 02

Start: LOAD s0, Acheck
     OUTPUT s0, LED_PORT

JUMP start
```

Use `kcpsm6.exe` to translate `.psm` into `.v` (**Tips:** `ROM_form.vhd` will generate **VHDL** file. If you are doing with **Verilog**, use `ROM_form.v` in `verilog folder`). After compilation, `xxx.v` will be created. In addition, `xxx.log` stores detailed information about the assembly code and its corresponding binary code. `xxx.hex` is the machine code represented in hex.


### STEP 3: Create the Hardware Part

Open `ise`(ise_14.7 as example) and create a new project for the design. **Nexys3** uses `Spartan-6` FPGA with device `XC6SLX16` and `CS324` package. Set `Preferred Language` to `Verilog`.

After creating the project, **import source files into the project**. **Right click** and select `Add Source` to import PicoBlaze and memory space into the project. Then create a new `Verilog file` to **connect PicoBlaze and memory space** together and implement other hardware part (e.g. input & output, control 7-seg with hardware). 

![VBVe78.jpg](https://s2.ax1x.com/2019/06/08/VBVe78.jpg)

![VBV9te.jpg](https://s2.ax1x.com/2019/06/08/VBV9te.jpg)

Copy paste the content in `design template file` to your `top module file` (new source file). The `design template file` includes **instantiated PicoBlaze (KCPSM6)** and **memory space**. You will need to change the **name of memory space** and set `.C_FAMILY` to `S6` and `.C_RAM_SIZE_KWORDS` to `1` following picture shows.

![VBVCfH.jpg](https://s2.ax1x.com/2019/06/08/VBVCfH.jpg)

The rest of part in design template file gives an example of how to connect input/output port with PicoBlaze and so on. Modify them with your need. In the example, no read input is needed but only output data and therefore the code can be modified as follows.

![VBVpkD.jpg](https://s2.ax1x.com/2019/06/08/VBVpkD.jpg)


### STEP 4: Simulation with Isim

Then you will need to run **simulation tools** `Isim` to make sure that the design works correctly. Firstly, you will need to create a `testbench` as following picture shows and **generates the input signals** your top module needed.

![VBVipd.jpg](https://s2.ax1x.com/2019/06/08/VBVipd.jpg)

Select `View` as `Simulation` and run simulation.

![VBVZ0f.jpg](https://s2.ax1x.com/2019/06/08/VBVZ0f.jpg)

Result is shown. As we can see, output (led) would always generates `10100111` which is the same as we expected. `00000000` is due to the fact that it takes time for processor to calculate the output result. Each instruction in PicoBlaze takes `2 clock cycle` to finish.

![VBVF1A.jpg](https://s2.ax1x.com/2019/06/08/VBVF1A.jpg)


### STEP 5: Generate Programming File and Download into FPGA Board

Once your design works correctly with simulation, you can generate a `programming file` and **download** it into the board. Select `Implementation` view and import `xxx.ucf` file (e.g. `Nexys3_master.ucf` in the example). This file helps you connecting your defined port with the port on the board. Modify `.ucf` file the picture shows. 

![VBVAXt.jpg](https://s2.ax1x.com/2019/06/08/VBVAXt.jpg)

![VBVVnP.jpg](https://s2.ax1x.com/2019/06/08/VBVVnP.jpg)

**Double click** `Generate Programming File` and in the end, synthesis tool will generates `.bit` file. Connect the board with USB cable and open `Adept`. Select the `bit file` and click `Program` to download the file into Nexys3 board. LED will light as you desired.

![VBVgAO.jpg](https://s2.ax1x.com/2019/06/08/VBVgAO.jpg)

![VBV2ND.jpg](https://s2.ax1x.com/2019/06/08/VBV2ND.jpg)


### REF

[1] KCPSM6_User_Guide_30Sept14.pdf

[2] Nexys 3 Reference Manual

[3] https://www.youtube.com/watch?v=7tRkpudwL2g
