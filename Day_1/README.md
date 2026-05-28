# Introduction to Verilog RTL Design & Synthesis
---
## Table of Content
 1. [Design, Testbench & Simulation](#design-testbench--simulation)
 2. [Iverilog & GTKWave](#iverilog--gtkwave)
 3. [Lab Demonstration (Iverilog & GTKWave)](#lab-demonstration-iverilog--gtkwave)
 4. [Yosys](#yosys)
 5. [Lab Demonstration (Yosys)](#lab-demonstration-yosys)

## Design, Testbench & Simulator
### Design
A **Design** is typically described using a Hardware Description Language (HDL) such as Verilog, VHDL, or SystemVerilog. In this workshop, we will use **Verilog**. The HDL code contains the behavioral description that defines how the corresponding design should function.
### Testbench
To verify whether a design is functioning correctly, we write HDL code that applies input stimuli to the design and observes the outputs. This HDL code is called a **Testbench**.

This is how design and testbench are related:
<div align="center">
  <img src=image.png alt="Design & Testbench Overview">
</div>

In testbench there is not external input and output.
### Simulator
A simulator is software that takes the design and testbench as inputs, executes the simulation, and generates the output in Value Change Dump (VCD) format.

## Iverilog & GTKWave
**Iverilog** is the simulator we are going to use in this workshop. The output file created by the **Iverilog** i.e VCD is going to be use by **GTKWave** to get UI of the input and output waveforms. 

Here is the flow diagram, how things will work:

<div align="center">
    <img src=iv_gtk_flow.png alt="Iverilog & GTKWave Flow Diagram">
</div>

## Lab Demonstration (Iverilog & GTKWave)
Here we have written the behaviour functionality of the MUX
```verilog
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
The following testbench:
```verilog
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
    #300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule

```
The following iverilog command need to execute to create VCD file:
```bash
iverilog -o good_mux good_mux.v tb_good_mux.v
./good_mux
```
This will create VCD file by name "tb_good_mux.vcd"

After this following gtkwave command:
```bash
gktwave tb_good_mux.vcd
```
## Yosys
Yosys is a Synthesizer. What is **Synthesizer**? Synthesizer is a tool used to convert **RTL** to **Netlist**. Netlist is the representation of the cirucit that describe how different hardware component and logic gates are connected to each other.

At the end we want to know cirucit representation of the design we have developed according to the specifications. Yosys take the **verilog file** of our design and **.lib** file and provide what hardware and logical gates does are design is going to use in actual cirucit.
 - **.lib** : It contain all the basic logical gates which we want to use in the cirucit representataion of our design. 
 - **Flavour**: Each logic gate in **.lib** file have different variations which we called flavour. This flavours are slow, medium and fast. According to our need.
## Lab Demonstration (Yosys)
To enter the Yosys environment use the following command:
```bash
yosys
```

Read the **.lib** in the yosys environment with the following command:
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Read the verilog file:
```bash
read_verilog good_mux.v
```

Select the top module that you want to synthesis:
```bash
synth -top good_mux
```

Generate the netlist:
```bash
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Write the verilog code for the netlist showing the basic logic gate
```bash
write_verilog good_mux_netlist.v
```

Use the following command to see the netlist diagram:
```bash
show
```
<div align="center">
    <img src=show_result.png>
</div>