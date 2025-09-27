# DAY 5
-----
## Conditional and Looping Constructs in Verilog
-----
## The `if` Construct 

The **`if`** statement is a conditional construct used to make decisions in Verilog. It checks one or more conditions and executes the corresponding block of code. It can be nested or combined with `else` and `else if` to create complex decision-making logic.

### Syntax

```verilog
if (condition1)
   statement1;
else if (condition2)
   statement2;
else
   statement3;
```

### Key Points

  * Use `if-else` when checking for ranges or multiple, non-exclusive conditions.
  * A missing final `else` in a combinational block may infer a **latch**, which is often undesirable.
  * This structure works well for creating **priority-based logic**, where the first true condition is executed and the rest are ignored.

### Incomplete if - 1

This example demonstrates what happens when a final `else` is omitted.

#### Verilog Code

```verilog
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```

#### Cautions ⚠️

  * The synthesis tool will infer a latch to hold the previous value when no condition is met.
  * This can lead to unexpected behavior and potential timing issues.
  * ![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/caution_1.png)

#### Waveform
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if_sim.png)
#### For Synthesis 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if_show.png)
### Incomplete "if" - 2

This example demonstrates what happens when a final `else` is omitted in a nested if-else.

#### Verilog Code

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

#### Waveform 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if2_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if2_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_if2_show.png)

-----

## The `case` Construct

The **`case`** statement is a multi-way branching construct. It compares an expression with a list of possible values (case items) and executes the statement associated with the first matching branch. It's often a cleaner and more efficient alternative to a long chain of `if-else if` statements.

### Syntax

```verilog
case (expression)
   value1: statement1;
   value2: statement2;
   default: statement_default;
endcase
```

### Key Points

  * Ideal for checking discrete, mutually-exclusive values of a signal, such as in a **multiplexer** or a **Finite State Machine (FSM)**.
  * Always include a **`default`** case in combinational logic to prevent latch inference.
  * **Variants:**
      * `casez`: Treats `z` or `?` values in the comparison as don't-cares.
      * `casex`: Treats both `x` and `z` values as don't-cares. (⚠️ **Not recommended** for synthesis as it can hide design bugs and lead to simulation-synthesis mismatches).


#### Incomplete `case` Example

Verilog code is missing a `default` statement.

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
#### Waveform 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_case_sim.png)
#### Caution
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/caution_2.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_case_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/incomp_case_show.png)

#### Partial `case` Example (Using `casez`)

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

#### Caution 
⚠️ Be careful with don't-cares (`z`); they can sometimes lead to unexpected logic optimization if not used correctly.
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/partial_case_assign_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/partial_case_assign_show.png)

### Bad `case` Example 

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
```
#### Waveform 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/bad_case_sim.png)
#### Caution 
⚠️ `casex` is dangerous. If an `x` from an uninitialized register matches a case item, it can cause a mismatch between simulation and the actual hardware behavior.
#### For Synthesis 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/bad_case_syn.png)
#### Synthesized Diagram 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/bad_case_show.png)
#### GLS
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/bad_case_gls.png)

### Complete `case` Example

This is the recommended, synthesizable style.

 ```verilog
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```
 #### Waveform 
 ![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/comp_case_sim.png)
 #### For Synthesis 
 ![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/comp_case_syn.png)
#### Synthesized Diagram 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/comp_case_show.png)

-----

## Looping Constructs 🔁

### `for` Loop

A procedural loop used inside `always` or `initial` blocks. In simulation, it executes sequentially. In synthesis, the loop is **unrolled**, meaning the synthesizer creates a parallel hardware copy for each iteration.

### Syntax

```verilog
for (initial_condition; stop_condition; update_step) begin
    // Procedural statements to be repeated
end
```

### Key Points

  * Must be used inside a procedural block (`always`, `initial`).
  * Executes sequentially during simulation but results in parallel hardware after synthesis.
  * Not a direct 1-to-1 hardware replication; the tool unrolls it to create the intended logic.

### `for` Loop Example 1 (Multiplexer)

  ```verilog   
   module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel , output reg y);
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
#### Waveform 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/mux_generate_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/mux_generate_syn.png)
#### Synthesized Diagram 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/mux_generate_show.png)
#### GLS
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/mux_generate_gls.png)

### `for` Loop Example 2 (De-Multiplexer)

  ```verilog   
   module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel , output reg y);
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
#### Waveform 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/demux_generate_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/demux_generate_syn.png)
#### Synthesized Diagram 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/demux_generate_show.png)
#### GLS
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/demux_generate_gls.png)

### `generate-for` Loop (for structural replication)

Used to create multiple instances of modules or logic blocks. This is a powerful feature for building regular structures like adders, register files, or memory arrays.

### `generate-for` Example (Ripple Carry Adder - RCA)

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


module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```
#### Waveform
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/rca_sim.png)
#### For Synthesis 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/rca_syn.png)
#### Synthesized Diagram 
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/rca_show.png)
#### GLS
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/336e07d8423e18f249bddd093deebe45e2586bea/week1/Day5/Day5_images/rca_gls.png)
