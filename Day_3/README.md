# Combinational & Sequential Optimizations
---
## Combinational Optimizations
Combinational Optimization is a techniques used to improve a combinational logic circuit without changing its functionality. There are different type of it:
 - Constant Propogation
 - Boolean Logic Minimizations

In the lab, we are going to focus on the Constant Propogation.

The following commmand must be executed in the yosys after the **synth -top <module>**.
```bash
opt_clean -purge
```

Let see some example:
```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```

Synthesis:
<div align=center>
    <img src=images\optc4_show.png>
</div>

```verilog
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 


endmodule
```

Synthesis:
<div align=center>
    <img src=images\mmopt_show.png>
</div>

```verilog
module sub_module(input a , input b , output y);
 assign y = a & b;
endmodule



module multiple_module_opt2(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module U1 (.a(a) , .b(1'b0) , .y(n1));
sub_module U2 (.a(b), .b(c) , .y(n2));
sub_module U3 (.a(n2), .b(d) , .y(n3));
sub_module U4 (.a(n3), .b(n1) , .y(y));


endmodule
```

Synthesis:
<div align=center>
    <img src=images\mmopt2_show.png>
</div>

## Sequential Optimizations
Sequential optimization in circuit synthesis refers to optimization techniques that consider both combinational logic and sequential elements. Here are some types:
 - Sequential Constant
 - State Minimzation
 - Retiming
 - Cloning

 We are going to focus on the Sequencit constant. The following must be execute after **synth** command.

```bash
difflibmap -liberty <timing_lib>
```

Few examples:
```verilog
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

Synthesis:
<div align=center>
    <img src=images\dff4_show.png>
</div>

Another example:
```verilog
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

Synthesis:
<div align=center>
    <img src=images\dff5_show.png>
</div>