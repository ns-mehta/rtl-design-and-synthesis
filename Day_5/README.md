# Optimization in Synthesis
---
In the previous module, we saw that synthesis of the HDL code is done very differently. So as designer it's important to know what HDL code corresponds with which synthesis, because our end motive is to have a actual design which can be fabricated and works according to our speicification. In this module we will work on that.
## Synthesis of "If-else" & "Case" Statement
### If-Else Statement
When **if-else** is used in HDL code, we all know the following order will be follow first **if** condition will be match then **else if** condition and at last **end** part will run. This looks very similar to being use by the mux. Here the circuit diagram how it will be done:
<div align=center>
    <img src=if_const.png>
</div> 

Conside the following HDL code of compelete **if-else** statement.
```verilog
module comp_if (input i0 , input i1 , input i2 , input i3, input i4, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;
	else
		y <= i4; 
end
endmodule
```
Here is the circuit diagram:
<div align=center>
    <img src=comp_if_show.png>
</div>
<div align=center>
    <img src=comp_if_dig.svg>
</div>

This will properly happen if in each condition what signal to be executed is properly mentioned. Consider in the HDL code we only use the **if** statment and didn't write anything about the **else** like the following HDL code.
```verilog
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```
The following will be the waveform for HDL simulation:
<div align=center>
    <img src=incomp_if_wave.png>
</div>

The synthesis for the HDL code will give the **Latch** or more specifically **Inferred Latch**
<div align=center>
    <img src=incomp_if_show.png>
</div>

This **inferred latch** is not good as it will provide unexpected results which will cause the trouble. Like level sensitive due to which there will be race out condition. This is a undesirable output.

Let's take another example:
```verilog
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```
HDL code will have following waveform output:
<div align=center>
    <img src=incomp_if2_wave.png>
</div>

The following will be the synthesis output:
<div align=center>
    <img src=incomp_if2_show.png>
</div>

### Case Statement
Case statement also work like **if-else** statement. But here the condition does not have the priority which is present in the **if-else** statement. If the proper case statment is provided, **Case** statment part in the HDL will synthesis according to this:
<div align=center>
    <img src=mux_multin.svg>
</div>

If following issue exist in the **Case** Statement, this will cause different synthesis
 - Missing Conditions
 - Overlaping Conditions
 - Few Conditions doesn't defining the complete output

 Let's see through the lab what might be the problem in the synthesis if HDL code is incomplete.

 Example:
 ```verilog
 module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
 ```
 Waveform:
 <div align=center>
    <img src=incomp_case_wave.png>
</div>

Synthesis:
<div align=center>
    <img src=incomp_case_show.png>
</div>

Example:
```verilog
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase
end
endmodule
```
Iverilog will send error for this code.

Synthesis of the HDL code:
<div align=center>
	<img src=parcase_show.png>
</div>

Example:
```verilog
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end

endmodule
```
Waveform:
<div align=center>
	<img src=badcase_wave.png>
</div>

## Looping Construct
We will see the use case of the looping construct in this section. We are going to disucss about the **for** loop here and it's two use case in the verilog.
 - Procedural **for** Loop
 - Generate **for** Loop

### Procedural **for** Loop
Used inside the **always** and **initial** block to represent the repeated behaviour or similar evalution. For example:
```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```
In the above code, without **for** loop we have to write statement for each condition, which seems to be alright for small code like here as we have to write only 4 condition. But where we have to write 64 condition, in that cases it will be difficult.

Let's see this HDL simulation waveform, synthesis of HDL and Gate Level Simulation:

HDL Simulation Waveform:
<div align=center>
	<img src=muxg_wave.png>
</div>

Synthesis:
<div align=center>
	<img src=muxg_show.png>
</div>

There the **Latch** here. But why? Because we have not provided the initial value of the signal. So synthesier is getting confused and putting latch to keep the previous value. If we define the value of the y before for loop then thinks work fine. But I also have doubt, if y is initialise inside always block. So whenever there is change in the signal, y must have initialize value for few time. But in HDL simulation and Gate Level Simulation it is not visible and Circuit synthesis gives the correct value.

The following change in the code:
```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
y = 1'b0;
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```

Synthesis:
<div align=center>
	<img src=muxg_up_show.png>
</div>

The corresponding GLS waveform:
<div align=center>
	<img src=muxg_up_net_wave.png>
</div>

Above we how the implementation of the **MUX**. Let's now try **DEMUX**
This is the **DEMUX** implementation using the **CASE** statement
```verilog
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
```

HDL Waveform:
<div align=center>
	<img src=demuxc_wave.png>
</div>

Synthesis:
<div align=center>
	<img src=demuxc_show.png>
</div>

Using the for loop:
```verilog
module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule
```

HDL Waveform:
<div align=center>
	<img src=demuxg_wave.png>
</div>

Synthesis:
<div align=center>
	<img src=demuxg_show.png>
</div>

### Generate **for** Loop
Used with a generate block to create multiple instances of hardware during elaboration.

Let see the example:

HDL code:
```verilog
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
```verilog
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```
HDL Waveform:
<div align=center>
	<img src=rca_wave.png>
</div>

Synthesis:

 - Full Adder:
<div align=center>
	<img src=fa_show.png>
</div>
 - Ripple Carry Adder
 <div align=center>
	<img src=rca_show.png>
</div>

GLS:
<div align=center>
	<img src=rca_net_wave.png>
</div>