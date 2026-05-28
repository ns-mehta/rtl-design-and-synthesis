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

## Yosys

## Lab Demonstration (Yosys)