---
layout:     post
title:      FPGA Serial I - Get Started with PicoBlaze
subtitle:   Run PicoBlaze on Nexys3 (Spartan-6 FPGA) to Light 8 LEDs
date:       2019-06-08
author:     yg
header-img: img/post-bg-5.jpg
catalog: true
tags:
    - How To's
    - FPGA
---


### OVERVIEW
This tutorial shows how to use Xilinx FPGA (Nexys3 board). In this example, we will run a soft core called PicoBlaze on Nexys3 and then light 8 LEDs. Code for this part is [here](https://github.com/yg9120/Nexys3/tree/master/LightLEDS)


### REQUIREMENTS
```
**Nexys3**:  FPGA Development Board with Xilinx Spartan-6 FPGA installed
**ISE**:     IDE tool used to develop FPGA applications
**Adept**:   Tool to download bit file into FPGA
**Verilog**: HDL to develop FPGA application
```


### STEP 1: Download PicoBlaze Source Code

Go to the [PicoBlaze Page](https://www.xilinx.com/products/intellectual-property/picoblaze.html#design) to download the `KCPSM6 ...` zip file. 

![VBETkF.jpg](https://s2.ax1x.com/2019/06/08/VBETkF.jpg)

**NOTE**: There are several helpful materials in the source code file. For example, `KCPSM6_User_Guide` includes **general design flow** with PicoBlaze **(P6-P13)** and explanations of each instruction and so on. `kcpsm6.exe` is the compiler for PicoBlaze. The **components and its connections** of PicoBlaze can be found in **user guide page 8**. 

After downloaded, open zip file and move into `Verilog folder`. In this folder, `kcpsm6.v` is the source code for PicoBlaze (also called `CDPSM6`). `ROM_form.v` would be used by `kcpsm6.exe` to generate the memory space. `kcpsm6_design_template.v` is also helpful even though it is only a template file. This is not a Verilog file that can be simulated or synthesis directly. 


### STEP 2: Write Assembly Code

The following instructions will light LEDs on Nexys3 with the position in 7, 5, 2, 1, 0. Edit and save them as `xxx.psm` file.

**NOTE**: PicoBlaze instruction set can be found in `KCPSM6_User_Guide`

```
CONSTANT Acheck, 10100111’b
CONSTANT LED_PORT, 02

Start: LOAD s0, Acheck
     OUTPUT s0, LED_PORT

JUMP start
```

Run `kcpsm6.exe` to translate `.psm` into `.v` (**NOTE:** `ROM_form.vhd` will generate **VHDL** file. If you are using with **Verilog**, use `ROM_form.v` in `verilog folder`).

After compilation, `xxx.v` will be created. In addition, `xxx.log` stores detailed information about the assembly code and its corresponding binary code. `xxx.hex` is the machine code represented in hex.


### STEP 3: Create New Project in IDE

Firstly, Open `ise`(ise_14.7 in this example) and create a new project. Select `Spartan-6` FPGA and `XC6SLX16`, `CS324` package. Set `Preferred Language` to `Verilog`.

After creating the project, **import source files into the project**: **Right click** and select `Add Source` to import PicoBlaze and memory into the project. Then create a new `Verilog file` to connect PicoBlaze and memory space and implement other hardware part (e.g. input & output, control 7-seg with hardware). 

![VBVe78.jpg](https://s2.ax1x.com/2019/06/08/VBVe78.jpg)

![VBV9te.jpg](https://s2.ax1x.com/2019/06/08/VBV9te.jpg)

Copy paste the content in `design template file` to your `top module file` (new created file). The `design template file` includes **instantiated PicoBlaze (KCPSM6) and memory space**. You will need to change the **name of memory space** and set `.C_FAMILY` to `S6` and `.C_RAM_SIZE_KWORDS` to `1` as picture shows below.

![VBVCfH.jpg](https://s2.ax1x.com/2019/06/08/VBVCfH.jpg)

The rest of part in design template file gives an example of how to connect PicoBlaze's input/output and so on. Modify them with your need. In the example, no input but only output is needed and therefore the code can be modified as follows.

![VBVpkD.jpg](https://s2.ax1x.com/2019/06/08/VBVpkD.jpg)


### STEP 4:Confirm the Correctness of the Design

Create a `testbench` and generate some input signals to provide in/out signal for top module as following picture shows.

![VBVipd.jpg](https://s2.ax1x.com/2019/06/08/VBVipd.jpg)

Run **simulation tool** `Isim`. Select `View` as `Simulation` and start the simulation.

![VBVZ0f.jpg](https://s2.ax1x.com/2019/06/08/VBVZ0f.jpg)

Here is the result. As we can see, output (led) always generate `10100111` which is the same as we expected. `00000000` is due to the fact that it takes time for processor to calculate the output. Each instruction in PicoBlaze takes `2 clock cycle` to finish.

![VBVF1A.jpg](https://s2.ax1x.com/2019/06/08/VBVF1A.jpg)


### STEP 5: Generate Bit File and Download into FPGA Board

Once your design works correctly with simulation, you can generate a `programming file` and **download** it into the board. 

Select `Implementation` view and import `xxx.ucf` file (`Nexys3_master.ucf` in our example). This file connects your defined port with the port on the board. Modify `.ucf` file as below picture shows. 

![VBVAXt.jpg](https://s2.ax1x.com/2019/06/08/VBVAXt.jpg)

![VBVVnP.jpg](https://s2.ax1x.com/2019/06/08/VBVVnP.jpg)

**Double click** `Generate Programming File` and Synthesis tool will generate `.bit` file. This may take a while. Connect the board with USB cable and open `Adept`. Select the `.bit` and click `Program` to download the file into Nexys3 board. 

After that, you will see LEDs light as you expected.

![VBVgAO.jpg](https://s2.ax1x.com/2019/06/08/VBVgAO.jpg)

![VBV2ND.jpg](https://s2.ax1x.com/2019/06/08/VBV2ND.jpg)


### REF

[1] KCPSM6_User_Guide_30Sept14.pdf

[2] Nexys 3 Reference Manual

[3] https://www.youtube.com/watch?v=7tRkpudwL2g
