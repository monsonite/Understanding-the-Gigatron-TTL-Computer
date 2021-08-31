## Understanding-the-Gigatron-TTL-Computer

![image](https://user-images.githubusercontent.com/758847/131498222-a7e9bcbf-f092-489f-b4e2-38d04f464eb4.png)

The Gigatron is an 8-bit Harvard machine with a single stage pipeline which allows one instruction to be executed on every clock cycle.


Moreover, it used a 16-bit long instruction word, with the instruction and addressing mode being contained in the upper byte and an 8-bit data word contained in the lower byte.


This combination gives considerable flexibility allowing page addressing, page branching and loading constants to be done within a single cycle.

The Gigatron architecture consists of 6 basic blocks of which the ALU is the largest element using about 30% of the logic.

The Harvard architecture dictates separate memory and buses are used for program and data and this was chosen as a convenient approach for a machine who's primary function is to send pixel data from RAM to the VGA interface. Whilst these memory spaces are considered separate, in the Gigatron they share some addressing resources, which leads to greater flexibility of addressing modes, especially useful for video generation. The Gigatron is sometimes referrred to as having a modified Harvard architecture.

ROM, Program Counter, Address Register.

The ROM is a 64K x 16-bit device which is addressed using the Program Counter PC.  The PC is made from four, 4-bit presettable, binary counters which normally increment on every clock cycle but can be preloaded when a local branch or a long-jump is required.  The upper 8 bits forms the page address and this is supplied from the Y register - a write only register. The lower 8-bits is supplied from the data bus, so a branch address may come from the data register, the accumulator or the input register.

The ROM supplies a 16-bit long instruction word, with the instruction and addressing mode being contained in the upper byte and an 8-bit data word contained in the lower byte.

This combination gives considerable flexibility allowing page addressing, page branching and loading constants to be done within a single cycle.

Both the instruction byte and the data byte are held in registers which provides a simple single stage pipeline mechanism.

RAM, Addressing Registers and Memory Addressing Unit.

The RAM can be either a 32K byte or 64K byte. 19,200 bytes of the RAM is used to hold the 160 x 120 pixel data and this is addressed using special X and Y registers - named because they define the (x,y) coordinates of a pixel. The Gigatron was designed with the primary purpose of generating video, where a stream of pixels is sent from memory to the VGA interface.  The X register consists of an 8-bit presettable counter, which can be incremented on every instruction cycle, allowing a row of pixel data to be output under software control. The X register can also be preset in software and this permits smooth, horizonal scrolling effects to be generated. The Y register is used to address a given row of pixel data. Manipulating X and Y in software allows a full frame of video data to be sent to the screen, updating at 60Hz framerate.

The Y register has a dual purpose, it is also used for long jumps within the program ROM. This gives a quick means of copying image data from ROM to RAM, as well as providing flexible ROM addressing modes.

Between the X and Y registers and the RAM is a multiplexer known as the Memory Addressing Unit or MAU. This selects whether the RAM will be addressed from the X and Y video addressing registers (during video output) or from the data byte $dd held in the lower byte of the instruction word. This feature provides a neat solution to program access to RAM allowing indirect addressing of pages of data memory. It also permits a bytecode virtual machine interpreter to be implemented using the Von Neumann model where program and data are placed in the same RAM space.

The ALU

The ALU is built entirely from combinational logic.  It has two 8-bit inputs for the operands, one 8-bit output for the result and 5 control lines that define the operation. An 8-bit register, the Accumulator, provides a temporary store for the ALU result.

In common practice, one of the 8-bit operand inputs of the ALU is connected to the Accumulator A, and the other operand is supplied from the Data Bus B. The ALU is hardwired logic designed to generate the logical or arithmetical  operation of the two input operands A and B. 

The ALU provides 4 logical operations: AND, OR, XOR and INVERT and the arithmetic operations ADD and SUBTRACT.  These are fundamental to almost every CPU. Left shift is done using addition and right shift is provided with the use of look up tables.

The Gigatron logic unit can generate the full 16 different Boolean operations of two binary inputs, but only a handful of these are actually useful. Four control inputs are used to select the useful ones as small subset of all combinations that are available. A further control input allow the carry input to the binary adder to be set.

The data bus supplies the second operand, which may be a fixed constant from ROM, the output of a RAM address or the value from the input (keyboard) register.

The output of the ALU is placed onto the Result Bus. From here the output can be written to the VGA output register OUT if it is pixel data, or it may be used to load the X register or the Y register. 

If the output is part of an arithmetical or logical operation for example  ADD A,B it is conventional to write it back to the Accumulator  AC, where it can be used in the next operation, or placed back onto the data bus so that it may be written to back memory.

The Control Unit CU

The Control Unit forms the heart of the machine and co-ordinates the operation of all the other sections of the machine. Principally it consists of an instruction decoder, which takes the 8-bit instruction input from the instruction register and generates 19 discrete control signals.  Various bit fields within the instruction word define the ALU or memory operation, the addressing mode and the source or destination of the data. 

Five control signals are used to set the operation of the ALU. One of these is the Carry In signal to the adder. The other four signals are used to set the arithmetic or logic operation - ADD, SUB, AND, OR, XOR, INVERT.

If the instruction is a conditional branch, the control unit determines whether the condition has been met, by testing the state of the Carry Out and the MSB of the Accumulator, and then forces a preload of the Program Counter.

Further control lines select the read and write operations of the RAM, the selection of the data source or destination on the data bus, and the loading of the X, Y, OUT and AC registers. 

The Control Unit is almost entirely a combinational logic circuit where the 8-bit instruction is decoded into the 19 control signals. However, certain signals including Write need to be synchronised to the second half cycle of the system clock.

Instruction decoding is done using a combination of 3 - 8 line and 2 - 4 line decoders and a diode ROM matrix. The diode ROM provides a simple and efficient means to generate a selection of arbitrary logic functions without having to use additional logic gates. 

Addressing Modes

The Gigatron provides a range of addressing modes for accessing its RAM.

Key:


x                 8-bit lower address byte of RAM

y                 8-bit upper address byte of RAM

$dd               8-bit data constant held in the lower byte of the instruction

[$dd]             8-bit Zero page addressing of the address pointed to by $dd

[x]               8-bit Zero page addressing of the address pointed to by x

[y,x]             16-bit  Absolute addressing of the byte 256y + x

[y,$dd]            16-bit  Absolute addressing of the byte 256y + $dd

[y,x++]           16-bit  Absolute addressing of the byte 256y + x, with post-increment of x

