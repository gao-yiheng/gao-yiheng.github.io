---
layout:     post
title:      Nexys3 Part III - Build a Microprocessor
subtitle:   Build Your Own Processor with Multi-Cycle Machine
date:       2019-06-06
author:     yg
header-img: img/post-bg-5.jpg
catalog: true
tags:
    - Computer Architecture
    - Verilog
    - FPGA
---


### OVERVIEW
In previous parts, we use PicoBlaze as micro-controller to implement our design. This part will try to **design our own microprocessor** which run on Nexys3. The processor is quite **simple but could handle simple tasks like generating Fibonacci numbers**. Code for implementing the microprocessor is [here](https://github.com/yg9120/Nexys3/tree/master/CreateYourMicroprocessor).


### STEP 1: Define Your Own Instruction Set

As mentioned before, the processor is very simple and therefore only supports **limited number of instructions**. The following picture shows the `instruction set` the processor supports and how it coded.

![VDjUER.jpg](https://s2.ax1x.com/2019/06/09/VDjUER.jpg)

An instruction is `18-bit` long. First `6-bit` are `op code` to **indicate the function of an instruction**. The **register file** has `16` registers and therefore, requires `4-bit` to **decode the register**. The **length of a register** is `8-bit` long so that **instance number size** could be `8-bit`.


### STEP 2: Design Micro-Architecture to Implement the Instruction Set

There are lots of ways to implement the instruction set. The simplest way is `single-cycle machine`. **Every instruction takes a clock cycle to execute**. The **disadvantage** for this machine is that **clock speed is limited by the longest execute time instruction**. To **increase clock speed**, `multi-cycle machine` can be applied. In this machine, the **instruction execution is divided into at most 5 part**: `instruction fetch (IF)`, `instruction decode (ID)`, `execute (EXE)`, `memory access (MEM)`, `write back (WB)`. **Different instruction requires different part to finish**. For example, `JUMP` requires `IF`, `ID`, `EXE` while `ADD sX, kk` needs `IF`, `ID`, `EXE`, `MEM`, `WB`. Each part in `multi-cycle machine` should be `enable` when needed and `disable` when finished in a clock cycle. In this way, an instruction might take `3` to `5` clock cycle to finish (clock cycle increased) but **maximum clock speed can be increased either** because now clock speed is limited by the part that takes longest time to execute. The `disadvantage` for `multi-cycle machine` is the **low usage rate**. Each part only works at 1 clock cycle. To overcome this, `pipeline` can be used but at the same time, the **complexity of the system is dramatically increased**.

Therefore, considering both `system complexity` and `clock speed`, `multi-cycle machine` is applied to implement the instruction set defined in STEP1. The following picture shows the high-level view of the microprocessor. `Instruction memory` puts the code we want to execute. The processor will `fetch` an instruction from instruction memory and `decode`, `execute` it.

![VDjd4x.jpg](https://s2.ax1x.com/2019/06/09/VDjd4x.jpg)

The following picture contains more detail about the microprocessor. As we can see, it contains a `PC module`, `Register File`, `ALU Unit` and `Control Unit`. `PC module` responsible for **calculating next address** that microprocessor would fetch from instruction memory. `Register File` includes **16 registers**, and the **size of a register** is `8-bit`. `ALU` in the design would **perform add and subtract operations**. `Control Unit` would **generate control signals** based on current state to control other components.

The `control unit` is implemented in `Mealy FSM`. An **instruction requires three to five clock cycles to finish**. The specific clock cycle time depend on instruction. The step an instruction execute is divided into **up five stages**: `Fetch`(instruction fetch), `Decode`(instruction decode), `Execute`(Instruction execute), `Alu`(if it’s ALU-related instruction), `Wback`(write result back to register file). Every control signal is generated based on current state and input (op_code) of control unit.

![VDjaU1.jpg](https://s2.ax1x.com/2019/06/09/VDjaU1.jpg)


### STEP 3: Prepare Test Code (Assembly)

To test the design of the processor, a program stored in instruction memory are needed. A code that **read switch information from FPGA board as input n, generate nth Fibonacci number and finally, output the result** has been implemented as `.psm` file as follows. Then a translation from assembly to machine code is needed. To make the translation simple, the instruction set was chosen from PicoBlaze instruction set. Therefore, you can use the translation tools in PicoBlaze package to translate your assembly code into machine code.

```
; Read input from port 1 which is defined as switch
; Saturate input up to 13 (n = input)
; Calculate nth fibonacci number and output to LEDs (port 3)

CONSTANT SWITCH_PORT, 01    ; hex by default
CONSTANT LED_PORT,    03

start: INPUT s0, SWITCH_PORT    ; Read 8-bit input
       COMPARE s0, 0D           ; if (n >= 13) 
	   JUMP C, gen_fib
	   LOAD s0, 0C              ; n = 12
	   
gen_fib: LOAD s1, 00            ; f1 = 0
         LOAD s2, 01            ; f2 = 1
		 
		   ADD s0, 01              ; make compensate		 
calculate: SUB s0, 01              
		   JUMP Z, out_result      ; while (n-- > 0)
		   
		   LOAD s3, s2            ; temp = f2
		   ADD s2, s1             ; f2 = f2 + f1
		   LOAD s1, s3            ; f1 = temp
		   JUMP calculate
		   
out_result: OUTPUT s2, LED_PORT
            JUMP start
```

### STEP 4: Implement the Micro-Architecture with Verilog

Implement the `micro-architecture` shown in STEP2 with `Verilog` code and insert your code into instruction memory. Code can be referred [here](https://github.com/yg9120/Nexys3/tree/master/CreateYourMicroprocessor).


### STEP5: Verify your design

After implemented the `micro-architecture`, you can `simulate` your design with a `testbench` you created in `Isim`. Send the input to your microprocessor and see if the microprocessor could generate the desired output. The following picture shows part of simulation result.

![VDjtb9.jpg](https://s2.ax1x.com/2019/06/09/VDjtb9.jpg)


### STEP6: Synthesis the Design and Download It Into Nexys3

After `verifying` your design, you can `synthesis`, `download` the design into Nexys3 and **play with it!**

The following picture is the running result for the microprocessor to generate nth Fibonacci number and display with four-digit seven-segment. The Fibonacci sequence starts with `1`(input `0` output `1`).

![VDj0C6.jpg](https://s2.ax1x.com/2019/06/09/VDj0C6.jpg)


### REF

[1] Xilinx, “KCPSM6 User Guide”.

[2] David A. Patterson and John L. Hennessy “Computer Organization and Design” China Machine Press, ISBN 978-7-111-45316-1, 2017.


