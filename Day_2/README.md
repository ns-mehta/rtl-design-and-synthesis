# Timing Libraiers, Synthesis Methods & Efficient Flop Coding
---
## Table of Contents
1. [Introduction to Timing Libraiers](#intorduction-to-timing-libraiers)
2. [Synthesis Methods](#synthesis-methods)
3. [Efficient Flop Coding](#efficient-flop-coding)

## Intorduction to Timing Libraiers
Timing libraries are files that describe the timing, power, and electrical behavior of standard cells, I/O cells, and macros. These libraries are essential for synthesis, STA (Static Timing Analysis), and physical design. They are usually delivered in Liberty (.lib) format.

## Synthesis Methods
### Hierarchical Synthesis
In this method, we divide our design into submodule according to specifications. The following are the advantage:
 - Code can be written well with proper distinction of the functionality.
 - Synthesis become efficient.
 - Debugging the design become faster

 **Example:**
 <div align="center">
    <img src=show_hier.png>
</div>

### Flattened Synthesis
In this method, we synthesis all the logical gates in one module rather than subdividing it into many.

The **"flatten"** is the command need to be executed to generate the **Flattened Syntehsis**.

**Example:**
<div align="center">
    <img src=show_flat.png>
</div>

## Efficient Flop Coding
There different types of Flip-Flop, we are going to work with the following:

### Asynchronous Reset D Flip-Flop
```verilog
module dff_asyncres ( input clk ,  input async_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
<div align="center">
<img src=dff_asyncres.png>
</div>


### Asynchronous Set D Flip-Flop
```verilog
module dff_async_set ( input clk ,  input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
	if(async_set)
		q <= 1'b1;
	else	
		q <= d;
end
endmodule
```
<div align="center">
<img src=dff_async_set.png>
</div>


### Synchronous Reset D Flip-Flop
```verilog
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
<div align="center">
<img src=dff_syncres.png>
</div>
