# Introduction to Gate Level Simulation & Synthesis-Simulation Mismatch
---
## Gate Level Simulation (GLS)
Gate Level Simulation is a simulation performed after **RTL Synthesis** using a netlist made of logic gates taking testbench used in the **RTL Simulation**.

In GLS, Gate Level Verilog Model must also be provided to test the initialisation and functionality of the gate. This can also include the **Delay Annotation** for each gate, so that we can check whether there is no problem occur.

### Why we need GLS?
 - Detect Reset and Initialization problems. RTL simulators may initialize signals differently than real hardware.
 - Verify the timing Behaviour

 ### Types of GLS
 - Zero-Delay GLS
 - Timing GLS

Let's see pratical example of how the overall GLS flow work with the following verilog design:
```verilog
module ternary_operator_mux(input i0, input i1, input sel, output y)
    assign y = sel?i1:i0;
endmodule
```
Execute the following command in the iverilog and gtkwave to get the RTL simulation of code:
```bash
iverilog -o ternary_operator_mux ternary_operator_mux.v tb_ternary_operator_mux.v
./ternary_operator_mux
gtkwave tb_ternary_operator_mux
```
Here are the following results:
<div align=center>
    <img src=tom_wave.png>
</div>

Now let's create the Gate Level code for our RTL design and then do the simulation and see whether both are same or not. The following are the yosys command:
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty  ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_net.v
```
Following will be the iverilog and gtkwave commands
```bash
iverilog -o ternary_operator_mux_net ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./ternary_operator_mux_net
gtkwave tb_ternary_operator_mux
```
Here are the following results:
<div align=center>
    <img src=tomn_wave.png>
</div>

So there is no mismatch between the RTL simulation and Synthesis Simulationl.

 ## Synthesis-Simulation Mismatch
 ### Missing Sensitivity List
 In RTL simulator, output only changes with change in the input, if that input changes is provided in the **Sensitivity List**. But in the **netlist**, sensitivity list is not considered.

 For Example:
```verilog
module mux(input in0, input in1, input sel, output reg y);
    always@(sel)
    begin
        if (sel) y = in1;
        else y = in2;
    end
endmodule
```
In this RTL code, the change in output will only occur during change in the select signal irrespective whether **in0** or **in1** is change or not. This in functionality wise work as **Double Edge Flop**

Let's compare the RTL simulation and Gate Level Simulation.

RTL Simulation:
<div align=center>
    <img src=bm_wave.png>
</div>
Gate Level Simulation:
<div align=center>
    <img src=bmn_wave.png>
</div>

```verilog
module mux(input in0, input in1, input sel, output reg y);
    always@(*)
    begin
        if (sel) y = in1;
        else y = in2;
    end
endmodule
```
In this RTL code, change in ouptut will occure whenever there is change in the input. **sel** signal will only select which input to be consider.

### Blocking & Non-Blocking Statement
 -  "=" : Whenever there are statments, using the blocking assignment the current assignment will execute first then later assignment will be exectued. In this, statement run **sequential**.
 - "<=" : Statements which use non-blocking assignment, all the statements run **concurrently**.

Let's see pratical example of how the overall GLS flow work with the following verilog design:
 ```verilog
 module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

RTL Simulation:
<div align=center>
    <img src=bc_wave.png>
</div>
Gate Level Simulation:
<div align=center>
    <img src=bcn_wave.png>
</div>