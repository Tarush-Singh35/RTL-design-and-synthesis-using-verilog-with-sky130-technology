# RTL-design-and-synthesis-using-verilog-with-sky130-technology

RTL design and synthesis using verilog with sky130 technology

![Verilog-flyer](https://user-images.githubusercontent.com/86368099/123649186-3053ad80-d847-11eb-82aa-4c59dcb70884.png)


A 5-day cloud-based virtual preparing workshop led by VSD-IAT from 23rd to 27th June. The connect to the workshop page can be found here. The following are brief, day-wise documentation about the subjects shrouded in the course, alongside my executions of the lab meetings.

Day 1 - Introduction to Verilog RTL Design and Synthesis

The Basics

Design:
The actual Verilog code or set of Verilog codes containing the intended functionalities of the hardware model to meet the required specs. Below is an example of a simple latch design written in Verilog HDL.

module latch (input clk , input reset , input d , output reg q);

always @ (clk,reset,d)

begin

	if(reset)
		q <= 1'b0;
	else if(clk)
		q <= d;
    
end

endmodule

Testbench:
A program used for the purposes of verifying the functional correctness of the design. In a test bench, stimuli (test vectors) are set up to check whether the design meets the required specifications. Below is an example test bench written written in Verilog to test the above latch design.

`timescale 1ns / 1ps

module tb_latch;

	// Inputs
  
	reg clk, reset, d;
	// Outputs
	wire q;

        // Instantiate the Unit Under Test (UUT)
	latch uut (
		.clk(clk),
		.reset(reset),
		.d(d),
		.q(q)
	);

	initial begin
	$dumpfile("tb_latch.vcd");
	$dumpvars(0,tb_latch);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	d = 0;
	#500 $finish;
	end

always #25 clk = ~clk;

always #30 d = ~d;

always #15 reset=0;

endmodule

![1-0](https://user-images.githubusercontent.com/86368099/123648651-bde2cd80-d846-11eb-897a-07de71501f86.png)

Note: The design may have at least 1 essential information sources and at least 1 essential yields, anyway a testbench doesn't have any essential information sources or yields

Simulator:
It is the tool used to simulate the design, and check for its adherence to the specifications. They can be used to apply the test bench to the design. The simulator tool used for this workshop is Icarus Verilog (Iverilog). A simulator works by looking for changes on the input signals, and evaluating the output signals only when a change in value is observed on the input. Below is the simulation flow for Iverilog.

![1-01](https://user-images.githubusercontent.com/86368099/123649967-cdaee180-d847-11eb-9d47-25d14c3ea66b.png)

Icarus Verilog is an execution of Verilog HDL and works as a compiler for Verilog recreation. At the point when a plan and testbench record is taken care of to this test system, it's anything but a VCD or Value Change Dump document. This VCD record holds information about the progressions in the sources of info and yields of the source plan. A Waveform Viewer device is utilized to see the substance of the VCD record in an outwardly conceivable way. For our lab meetings, the device utilized is GTKWave, which is a GTK+ based wave viewer tool

Setting up the Lab Environment

In order to set up the tool flow and files for running the lab sessions, the following commands are used.

mkdir vsd

git clone https://github.com/kunalg123/vsdflow.git

git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git


These should add the necessary directories for the lab environment, including the sky130 standard cell libraries, its standard cell verilog models, as well as the source design and testbench verilog files for the lab sessions. Once the git cloning is succesful, the following base directories should be available on your file system.

Exploring Iverilog and GTKWave

To understand how to use these tools, lets explore Iverilog and GTKWave using an example of a simple 2:1 Multiplexer from the provided verilog_files directory. Let us take a look at the source verilog code for the design and testbench files, labeled as good_mux.v and tb_good_mux.v respectively.

![1-7](https://user-images.githubusercontent.com/86368099/123652179-b2dd6c80-d849-11eb-81e4-ff7c92d29d98.png)

Fig.: Verilog code for 2:1 Multiplexer Design

![1-6](https://user-images.githubusercontent.com/86368099/123652504-fcc65280-d849-11eb-8475-447ca2f4cacc.png)

Fig.: Testbench for 2:1 Multiplexer

To simulate these files in Iverilog, the following command can be used.

iverilog good_mux.v tb_good_mux.v

If done correctly, Iverilog should create an output file by the name of a.out which will be added in the file directory. To generate the VCD file, we must execute the a.out file.

Upon execution, a VCD file with the file extension .vcd will be generated. In our case, it is called tb_good_mux.vcd as that is the name specified in our test bench file. To view this VCD file in GTKWave, the following command is issued.

gtkwave tb_good_mux.vcd

Finally, in the GTKWave interface panel, we can add the unit under test and select the inputs and outputs whose waveforms we want to view. Now we can confirm if the waveform of our 2:1 Multiplexer matches the design specifications or not. As visible from the below waveform, it does athere to the design specifications.

![2to1 Mux netlist](https://user-images.githubusercontent.com/86368099/123653339-ac9bc000-d84a-11eb-9eb6-1e62ca905581.png)
![1-5](https://user-images.githubusercontent.com/86368099/123653469-ca692500-d84a-11eb-9142-03c8898ea92b.png)

Introduction to Yosys

Yosys is a framework for Verilog RTL synthesis. A synthesizer is a tool used for converting RTL based verilog code to netlist. RTL is the behavioural representation of the required specification in Verilog HDL. Netlist is the representation of the design in the form of standard cells present in the library. The Yosys synthesizer flow is as follows.

![1-8](https://user-images.githubusercontent.com/86368099/123653899-2df35280-d84b-11eb-926a-60a1ddbf935b.png)

Yosys makes use of the commands read_verilog to read the verilog design, read_liberty to read the .lib, and write_verilog to write the netlist file.

To verify the synthesis output, we can follow the same procedure as we did when verifying verilog design as the netlist must obey the same specifications as the original RTL design. In order to do this, we can pass the netlist file along with the original RTL testbench to our simulator and generate the VCD file. This VCD file can be viewed in the waveform viewer to confirm the behaviour of the synthesized netlist. This is shown below.

![1-9](https://user-images.githubusercontent.com/86368099/123654057-554a1f80-d84b-11eb-97b0-a21ef5907f7d.png)

Libraries and their Significance

A synthesizer conducts RTL to Gate level translation, wherein the behavioural design is converted to basic gates using the standard cell libraries provided, and connections are made between these gates. Libraries (.lib) are a collection of basic logic modules to implement any boolean logical functionalities, and may contain different flavours of the same gate such as 2-input/3-input or fast/slow

Need for Fast Cells:

For a digital logic circuit, the combinational delay in the logic path determines its maximum speed of operation. Lets take an example of a basic combinational circuit shown below with two D Flip-flops and some combinational circuit betwen them, where CLK is the clock signal and DFF B holds the output of the circuit.

![1-10](https://user-images.githubusercontent.com/86368099/123654246-8165a080-d84b-11eb-9372-9d93e8f41d44.png)

In this cicruit, the minimum size of 1 clock cycle is determined with the following relation

TCLK > TCQ_A + TCOMBI + TSETUP_B
where,
            TCQ_A is the propogation delay of DFF A
            
            TCOMBI is the propogation delay of the combination circuit
            
            TSETUP_B is the setup time for DFF B (min. time before the clock edge that input data must be supplied)

Hence, for maximum performance we need a smaller value of TCLK, which can be achieved by using faster cells to reduce the value of TCOMBI as much as possible.

Need for Slow Cells:

In order to prevent any hold violations, we need cells that work slower. If we consider the above example, there must be a minimum amount of time during which the output of the combinational circuit must be stable after the active edge of the clock, for DFF B to reliably capture the data at its input. This minimum delay is known as the Hold Time of the circuit. Hence, the following condition must be sastisfied to prevent hold violations.

THOLD_B < TCQ_A + TCOMBI

Thus, we need fast cells to meet performance requirements as well as slow cells to meet hold times in the .lib collection. To pick appropriate cells, the user must offer "constraints" to the synthesizer.

Note: As the primary load in a digital circuit is capacitance, the charge/discharge times of the capacitor decides cell delay. To discharge capacitors fast we need transistors capable of sourcing more current, thus needing wider transistors with more area and power requirements. While slower cells need narrow transitors with less area and power requirements.

Exploring Yosys and SKY130PDKs

Let us explore Yosys and the SKY130 libraries using the same example of the simple 2:1 Multiplexer from the previous sections. To start yosys, we must use the command yosys in the terminal. Once invoked, the yosys prompt should appear.

First, we must read the SKY130 libraries using the command read_liberty -lib filepath. Next, we must read the design using the command read_verilog filename.v. We must now specify the module name of the design we are synthesizing using the command synth -top modulename.

Once this is done, we can generate the netlist using the command abc -liberty your_library_filepath. For our case, this would be abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib. Succesfully executing this command should return the following report in the yosys prompt.

As visible in the report, yosys has found 3 inputs, 1 ouptut and 0 internal connections. This holds true for our example of the 2:1 Multiplexer. Further, yosys also mentions the cells used in the logic realisation. To observe a graphical view of the realisation, the command show can be used. This should generate the following graphic.

![yosys synthesis logic realization](https://user-images.githubusercontent.com/86368099/123657603-909a1d80-d84e-11eb-8e21-23e19003a595.png)

Finally, we can write the netlist using the command write_verilog -noattr filename.v. Here, the property "-noattr" is used to prevent yosys from dumping extra information in the final netlist file. Let's name our file as good_mux_netlist.v and execute the command. The final netlist represention is shown below.

![2to1 Mux netlist](https://user-images.githubusercontent.com/86368099/123657893-db1b9a00-d84e-11eb-8596-c459f564529a.png)





Day 2 - Timing libs, Hierarchical vs. Flat Synthesis and Efficient Flop Coding Styles

Further Information on .lib

The SKY130 library file used for this workshop is sky130_fd_sc_hd_tt_025C_1v80.lib

fd is SkyWater Foundry

sc is Standard Cell

hd is High Density

tt is Typical Process

025C is 25°C operating Temperature

1v80 is 1.8V operating Voltage

![cell knowledge of and gate](https://user-images.githubusercontent.com/86368099/123659526-69dce680-d850-11eb-9285-6298c45aefac.png)
![Cell contains and leakage power with input](https://user-images.githubusercontent.com/86368099/123659578-78c39900-d850-11eb-9902-9a4aed9587e0.png)

Here, you can see that the library provides details like technology (CMOS), power parameters, voltage parameters, current draw, area, timings and delays, etc. This file hold information on every standard cell provided in the library, along with all its flavours

Hierarchical vs. Flat Synthesis

When synthesizing a design containing multiple modules, the notion of heirarchical vs. flat design comes up. To understand their differrences, let's compare the two using an example. Below is a design that instantiates two low level modules, namely an OR gate (sub module u2) and an AND gate (sub module u1). This file is available under the verilog_files directory as multiple_modules.v
  
  ![1-2](https://user-images.githubusercontent.com/86368099/123663232-d5748300-d853-11eb-8389-4fdafba14074.png)
  
   let us synthesize this in Yosys with the following commands.
   
   yosys
   
read_liberty -lib .../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog multiple_modules.v

synth -top multiple modules

abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

If we see the graphical view of the realisation using the command show multiple_modules, we can notice that the actual OR and AND gates are not visible but only the sub modules u1 and u2 are shown. This is known as Heirarchical Synthesis as the heirarchies are preserved. This is shown below.

![multiple module yosys](https://user-images.githubusercontent.com/86368099/123663869-7105f380-d854-11eb-8df5-b6bc75001f0f.png)

If we generate the netlist using write_verilog -noattr multiple_modules_h_netlist.v, we see a similar story. The heirarchies are preserved, as there is one instantiation of each sub module under multiple_modules. The netlist is shown below.

![multiple module netlist](https://user-images.githubusercontent.com/86368099/123664044-9eeb3800-d854-11eb-9474-0cc1afb19eca.png)

To avoid heirarchical synthesis, we can use the command flatten in the yosys prompt after the synth command. Now, we can generate the netlist again using the command write_verilog -noattr multiple_modules_f_netlist.v. This is shown in the image below.

![1-7 (1)](https://user-images.githubusercontent.com/86368099/123664943-71eb5500-d855-11eb-9358-bd789b1aaee2.png)

The netlist generated here does not contain instantiations of sub modules or any heirarchical structure. It directly contains one module with mutiple standard cells. This is known as Flat Synthesis.

If we view it graphically using the show command, we can observe only standard cell implementations and no heirarchy present.

![multiple module flatten](https://user-images.githubusercontent.com/86368099/123665170-a3fcb700-d855-11eb-9625-7ee3712baae3.png)

Note: From a multiple module design file, it is possible to just synthesize a single sub module. This is done using the command synth -top sub_module_name, and is known as Sub Module level Synthesis. This is used when,

we have multiple instances of the same module and just want to synthesize it once, then replicate it however many times.

we are using a divide and conquer approach (used in massive designs when the tool does not do an appropriate or well optmised job).

Flop Coding Styles

In combinational circuits, each circuit element or cell experiences a time delay for the output to change based on a change in the input. This delay is known as Propogation Delay. Due to these propogation delays, the circuit might experience unwanted transitions in the output, espescially as the propagation delay stacks additively as the number of combinational circuits increase. These unwanted transitions are known as Glitches in the output.

To avoid glitches, we make use of D Flip-flops as storage elements or buffers in between the different combinational circuits. D flip-flops store the value present on their input, and its output changes only at clock edges. This brings stability between combinational circuits as the D flip-flops shield the combinational circuit they are feeding against glitches in their input, allowing the output of that combinational circuit to settle down.

Flip-flops come in various types. These are mainly:

Synchronous vs. Asynchronous set

Synchronous vs. Asynchronous reset

Rising (positive) edge triggered vs. Falling (negative) edge triggered

To further understand understand the different flop styles, let's look at 3 D flip-flops available to us in the directory verilog_files.

Note: For synthesizing designs involving D flip-flops in Yosys, we must use the command dfflibmap -liberty dff_library_filepath which in our case is dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib. This command is used to read the dff standard cells as some libraries may have seperate .lib files for these. The command must be issues after synth and before abc

 Rising edge D Flip-flop with asynchronous reset
 
module dff_asyncres ( input clk ,  input async_reset , input d , output reg q );

always @ (posedge clk , posedge async_reset)

begin

	if(async_reset)
  
		q <= 1'b0;
    
	else
  
		q <= d;
    
end

endmodule

![FF gtkwave async details](https://user-images.githubusercontent.com/86368099/123668521-ce9c3f00-d858-11eb-8959-64aaa4ba7d4f.png)

![FF yosys syenthesis](https://user-images.githubusercontent.com/86368099/123668671-f4c1df00-d858-11eb-98bd-e7a1d3584ea2.png)


Rising edge D Flip-flop with asynchronous set

module dff_async_set ( input clk ,  input async_set , input d , output reg q );

always @ (posedge clk , posedge async_set)

begin

	if(async_set)
  
		q <= 1'b1;
    
	else
  
		q <= d;
    
end

endmodule

 
 ![FF gtkwave async set details](https://user-images.githubusercontent.com/86368099/123667764-15d60000-d858-11eb-8cfc-e18d61f88d12.png)
 
 ![FF yosys syenthesis set](https://user-images.githubusercontent.com/86368099/123668013-4f0e7000-d858-11eb-9d89-f243d4f11fd4.png)
 

 Rising edge D Flip-flop with synchronous reset
 
 module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
 
always @ (posedge clk )

begin

	if (sync_reset)
  
		q <= 1'b0;
    
	else
  
		q <= d;
    
end

endmodule

![1-11](https://user-images.githubusercontent.com/86368099/123669226-7dd91600-d859-11eb-91e9-eb7f6c2367ec.png)

![FF yosys syenthesis sync](https://user-images.githubusercontent.com/86368099/123669357-9ba67b00-d859-11eb-86ed-01330f80ff62.png)

Some Interesting Optimisations for Special Cases

Case 1:

Let's consider the following design where the 3 bit input is multiplied by 2 and the output is a 4 bit value.

module mul2 (input [2:0] a, output [3:0] y);

	assign y = a * 2;
  
endmodule

Here, the ouput y[3:0] is nothing but the input a[2:0] appended with a 0 at the LSB. Or, we can say that y = {a, 0}. If we synthesize the netlist and look at its graphical realisation, we will see the same optimisation occuring in the netlist.

![1-15](https://user-images.githubusercontent.com/86368099/123672732-4d937680-d85d-11eb-8dd0-058f6d301acb.png)

Case 2:

Let's consider the following design where the 3 bit input is multiplied by 9 and the output is a 6 bit value.

module mult8 (input [2:0] a , output [5:0] y);

	assign y = a * 9;
  
endmodule

Here, the ouput y[5:0] is nothing but the input a[2:0] appended with itself. Or, we can say that y = {a, a}. If we synthesize the netlist and look at its graphical realisation, we will see the same optimisation occuring in the netlist.

![2-16](https://user-images.githubusercontent.com/86368099/123672904-7d427e80-d85d-11eb-92f6-880787436860.png)

Day 3 - Combinational and Seqeuntial Optimisations

Introduction to Logic Optimisations

There are broadly two types of logic available, combinational and sequential. In order to save cost by reducing power and area consumptions of a design, we must optimise the design as best as possible. For each type of logic, there exist different methods of opimisation, as follows.

1. Combinational optimisation methods:

Squeezing the logic for Area and Power savings

Constant Propogation

Direct Optimisation

Boolean Logic Optimisation

K-Map

Quine-McKluskey Algorithm

2. Sequential optimisation methods:

Basic

Sequential Constant Propogation

Advanced

State Optimisation

Retiming

Sequential Logic Cloning (Floor Plan Aware Synthesis)

Combinational Logic Optimisations

Let's take a look at some examples of combinational optimisations using the files opt_check.v, opt_check2.v, opt_check3.v, opt_check4.v, and multiple_modules_opt.v. All of these files are under the verilog_files directory.

Example 1: opt_check.v

module opt_check (input a , input b , output y);

	assign y = a?b:0;
  
endmodule

Before realising the netlist, we must issue a command to yosys to perform constant propogation and optimisations. this can be done using the opt_clean -purge command

After this step, we can continue as usual with abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib and write_verilog -noattr opt_check_netlist.v commands. If we view the graphical realisation witht the show command, we can see that Yosys has synthesized an AND gate as expected.

![3-2](https://user-images.githubusercontent.com/86368099/123673423-140f3b00-d85e-11eb-97e4-78468ec0a5aa.png)

Example 2: multiple_module_opt.v

module sub_module1(input a , input b , output y);

	assign y = a & b;
  
endmodule

module sub_module2(input a , input b , output y);

	assign y = a^b;
  
endmodule

module multiple_module_opt(input a , input b , input c , input d , output y);

wire n1, n2, n3;

sub_module1 U1 (.a(a), .b(1'b1), .y(n1));

sub_module2 U2 (.a(n1), .b(1'b0), .y(n2));

sub_module2 U3 (.a(b), .b(d), .y(n3));

assign y = c | (b & n1); 

endmodule

To optimise this design, we must use Flat Synthesis as otherwise the optimisations will not be performed on the sub module level. Thus, we must use the flatten command.

![3-6](https://user-images.githubusercontent.com/86368099/123674404-35245b80-d85f-11eb-8056-13ebc8d32512.png)

Once the optimisations are conducted by Yosys, we can observe that we only need a single standard cell to realise a design that originally contained multiple modules.

Sequential Logic Optimisations

Let's take a look at some examples of sequential optimisations using sequential constant propogation. We shall be using the files dff_const1.v and dff_const5.v. All of these files are under the verilog_files directory.

module dff_const1(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)

begin

	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
    
end

endmodule

At first glance, it may seem that the output bit q should be equal to an inverted reset or !reset. However, as the reset is synchronous, so the output depends on both the reset and clk edge. This can be confirmed by simulating the design in Iverilog, and viewing the VCD with GTKWave as follows.

![3-8](https://user-images.githubusercontent.com/86368099/123675110-fba02000-d85f-11eb-9820-b573e71f46b9.png)

If we observe the waveform above, when reset becomes 0, q only becomes 1 at the next clock edge. Hence, we do not get a sequential constant, and no optimisations should be possible here.

We must use the command dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib as our design includes D flip-flops. We can then generate the netlist using abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib and write_verilog -noattr dff_const1_netlist.v. To view the graphical realisation, we use the show command.

![3-10](https://user-images.githubusercontent.com/86368099/123675269-25594700-d860-11eb-9256-880326179520.png)

Example 2: dff_const5.v

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

Again, we might expect this design to be easily optimised as q = !reset. However as the design uses a synchronous reset, as well as due to propogation delay time of D flip-flop q1 (similar to example 3), we cannot replace the flip-flops. Hence, we do not get sequential constants. This can be viewed in the simulated waveforms as well.

![3-14](https://user-images.githubusercontent.com/86368099/123675514-70735a00-d860-11eb-945a-bde71a18d78f.png)

Since both D flip-flops cannot be replaced, no optimisation should be possible in this design. Let's look at the Yosys graphical realisation for this file.

![3-18](https://user-images.githubusercontent.com/86368099/123675597-897c0b00-d860-11eb-8608-94102e501b22.png)

no optimisations could be conducted by Yosys.

Day 4 - Gate Level Simulation, Blocking vs. Non-Blocking Assignments and Synthesis Simulation Mismatch

Introduction to Gate Level Simulations

Gate Level Simulation (GLS) is the process of executing the netlist as the unit under test, instead of the source design file as we conducted up until now. We can make use of the original testbench itself to simulate the behaviour of the netlist, as the netlist is logically the same as the source RTL code.

GLS is an important procedure as we must ensure that the netlist meets the design specifications. Unlike the RTL code which uses logic, the netlist utilizes physical gates to realise the same logic. These gates introduce timing concerns like propogation delays and hold times. Hence, using gate level simulations, we can ensure that the timings of the design are met as per the specifications.

The flow for gate level simulations using Iverilog is shown below.

![4-0](https://user-images.githubusercontent.com/86368099/123675889-d95ad200-d860-11eb-9b16-c18c354c90e9.png)

Note: For timing validation, we must run GLS with gate level models that are delay annotated (Timing Aware GLS).

Understanding Synthesis Simulation Mismatches

If the netlist is a true representation of the RTL code, then why must we verify the functionality of the netlist? This is because sometimes there are mismatches between the pre-synthesis simulations and the post-synthesis simulations. This is known as synthesis simulation mismatch and can occur due to, but not limited to, the following reasons:

Missing sensitivity list

Blocking and non-blocking assignments

Non-standard verilog coding

Missing Sensitivity List:

As we have learnt earlier, a simulator only updates or changes its output when it finds a change in the input. The verilog code below describes a 2:1 Multiplexer. There are 3 inputs i0, i1, and sel and 1 output y.

module mux(input i0, input i1, input sel, output reg y);

always @(sel)

begin

	if (sel)
	begin
		y = i1;
	end
	else 
	begin
		y = i0;
            
	end
  
end

endmodule

In this example the simulator checks for changes in the input sel to update the value of the output y, as the always block is evaluated only for sel. If sel were constant at 0 and i0 were to change its value, we would observe no change in the output y. This means the design would function more like a latch instead of a multiplexer. If we were to synthesize this design however, we would observe a multiplexer in our netlist instead of latch like behaviour. This would cause synthesis simulation mismatch

This can be fixed by replacing the always @(sel) line with always @(*) instead which looks for changes in any input.

Blocking and Non-Blocking Statements:

Within the always block in a verliog code, assignments can be of two types:

Blocking statements:

These are assignments that make use of the = operator.

Here, statements are always executed in the order that they are written.

Non-blocking statements:

These are assignments that use the <= operator.
Here, all RHS blocks are evaluated parallelly when the always block is entered, and then assigned to the LHS.
Let's look at some examples of blocking statements and how they can cause synthesis simulation mismatches.

Below, we have verilog code for a serial shift register with input d and output q1. As the code using blocking statements, the line q0 = d; will execute before q1 = q0;. This means that the value at d will direectly be updated at q1 at every clock edge, and the design will function as a single D flip-flop instead of a 2 bit shift register.

module code (input clk, input reset, input d, output reg q1);

reg q0;

always @(posedge clk,posedge reset)

begin

   if(reset)
   
   begin
   
   	q0 = 1'b0;
       	q1 = 1'b0;
   end
   
   else
   
   begin
   
       	q0 = d;
   	q1 = q0;
       
   end
   
end

endmodule

This problem will cause a mismatch in the pre-synthesis and post-synthesis simulations as the netlist will still function as a 2 bit shift register. This can be fixed by either writing the line q1 = q0; before the line q0 = d; or by using non-blocking statements instead as now both q0 and q1 would be evaluated parallelly regardless of their position. Hence, the non-blocking operator <= is preferred when using sequential designs.

Let's look at another example. Here we have the verilog code for some combinational logic.

module code (input a, input b, input c, output reg y);

reg q0;

always @(*)

begin

	y = q0 & c;
	q0 = a|b;
        
end

endmodule

Similar to the earlier example, the AND assignment would occur before the OR assignment. This means the output y would get a delayed value of a|b, as the value of q0 would get updated after the output y is updated. This too would cause synthesis and simulation mismatches, and can be fixed by writing the line q0 = a|b; before y = q0 & c;.

Exploring GLS and Mismatches with Iverilog

To further understand gate level simulations and mismatches between pre-synthesis and post-synthesis simulation, let us try some examples in Iverilog.

Example 1:

Below is the verilog code for a 2:1 multiplexer using a ternary operator. Let's attempt gate level simulations on this design.

module ternary_operator_mux (input i0 , input i1 , input sel , output y);

	assign y = sel?i1:i0;
  
  First, we must synthesize this file using Yosys and generate its netlist. We can view the graphical realisation of the same below.

!(ternarymux show)[images/Day4/4-1.png]

Now that we have the netlist, we can run the GLS using Iverilog by specifying the gate level models with the following command.

iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v

We can view the simulated waveforms using GTKWave and verify that the generated netlist does behave like a 2:1 multiplexer.
endmodule

![4-3 (1)](https://user-images.githubusercontent.com/86368099/123684598-4d01dc80-d86b-11eb-8625-9e9d5398352c.png)

Day 5 - If, Case, For Loop and For Generate

IF Constructs

Similar to regular programming languages, if statements in verilog also follow certain priority logic. But, verilog is a hardware description language, so these conditions directly affect the physical gates synthesized. Incorrect usage of if conditions can cause certain optimisation issues in the synthesis of a design.

Let's look at a template for an if block. Here we have an output y that gets assigned to certain statements bases on if conditionals

if (cond_1)

begin

	y = statement_1;
  
end

else if (cond_2)

begin

	y = statement_2;
  
end

else if (cond_3)

begin

	y = statement_3;
  
end

else

begin

	y = statement_4;
  
end

In this code block, we would assume that the synthesized netlist might contain a single multiplexer. However, the first if statement holds the highest priority in this block of code. This means that if cond_1 is satisfied, we do not enter the next if statements. Thus we get a ladder like multiplexer structure in the final design instead of a single multiplexer, which is shown below.

![5-0](https://user-images.githubusercontent.com/86368099/123685953-e1b90a00-d86c-11eb-9e71-b36e879cda48.png)

Another issue that can arise with if statements is when we leave them incomplete. Incomplete if statements occur when if blocks do not end with an accompanying else block. This is seen in the verilog code block below.

if (cond_1)

begin

	y = statement_1;
  
end

else if (cond_2)

begin

	y = statement_2;
  
end

Here, we have not specified what happens if both cond_1 and cond_2 are false. This is an incomplete IF statement. When the synthesizer comes accross this, it will add an Inferred Latch in the final synthesis. This can be seen in the diagram below

![5-2](https://user-images.githubusercontent.com/86368099/123686194-2c3a8680-d86d-11eb-99ca-c1b59b18b0ad.png)

Since the tool does not know what to do when both conditions are false, it will infer a latch to store the latest value of the output. When both conditions are false, the stored value in the latch will be driven to the output.

We must always take into consideration what hardware will our verilog code directly be translated to. Sometimes however, incomplete if constructs are perfectly fine in cases such as counters where latches must store the previous output as the input when no enable condition is found.

CASE Constructs

Let's look at the following verilog code block. Here, the inferred hardware would be a 4:1 multiplexer. Note that case statements do not have prioiry logic like if statements.

always @(*)

begin

	case(sel)
		2'b00: begin
		       y = statement_1;
		       end
		2'b01: begin
		       y = statement_2;
		       end
		2'b10: begin
		       y = statement_3;
		       end
		2'b11: begin
		       y = statement_4;
		       end
	endcase
  
end

Incomplete CASE.

always @(*)

begin

	case(sel)
		2'b00: begin
		       y = statement_1;
		       end
		2'b01: begin
		       y = statement_2;
		       end
	endcase
  
end

This occurs when some cases are not specified inside the case block. For example, if the 2'b10 and 2'b11 cases were not mentioned, the tool would synthesize inferred latches at the 3rd and 4th inputs of the multiplexer. To avoid this, we can make use of the default: case inside the case block so that the tool knows what to do when a case that is not specified occurs.

always @(*)

begin

	case(sel)
		2'b00: begin
		       x = a;
		       y = b;
		       end
		2'b01: begin
		       x = c;
		       end
	      default: begin
	      	       x = d;
		       y = d;
		       end
	endcase
  
  In the above example, we have 2 outputs x and y. This will create two 4:1 multiplexers, one for each output. If we look at case 2'b01, we have specified the value of x for this case, but not the value of y. We might assume that it is okay to do so, as a default case is specified for both the outputs, and if we don't directly specify the value of y, the tool will imply the default case. This, however, is incorrect. In partial assignments such as this, the tool will infer a latch at the 2nd input for multiplexer y as no value is specified.
  
  
end

Overlapping cases

always @(*)

begin

	case(sel)
		2'b00: begin;
		       y = a;
		       end
		2'b01: begin
		       y = b;
		       end
		2'b10: begin
		       y = c;
		       end
		2'b1?: begin
		       y = d;
		       end
	endcase
  
end

In the above code block, 2'b1? specifies that the LSB can be either 0 or 1. This means when the sel input is holding a value 3, conditions 3 and 4 both hold true. If we used an if condition here, due to priority logic, condition 4 would be ignored when condition 3 is met. However, in the case statement, both conditions would hold true as there is no priority logic, and we would get an unpreidctable output. This is known as an overlapping case.

Synthesizing Incorrect IF and CASE Constructs

Example 1:

Below is the file titled incomp_if.v, and can be found in the directory verilog_files.

module incomp_if (input i0 , input i1 , input i2 , output reg y);

always @ (*)

begin

	if(i0)
		y <= i1;
    
end
endmodule

The code contains an incomplete if statement as no else condition is mentioned. As we have learnt, we should see latch like behaviour in the simulation. Let's simulate this design using the following commands.

![5-4](https://user-images.githubusercontent.com/86368099/123687159-39a44080-d86e-11eb-931c-bf6f6894473a.png)

From the above waveform, we can observe that when i0 vecomes low, the output y holds the previous value of input i1. This shows latch like behaviour, and can further be detailed by looking at the synthesis output using yosys

![5-5](https://user-images.githubusercontent.com/86368099/123687248-550f4b80-d86e-11eb-8a19-d31934b92685.png)

Introduction to Looping Constructs

There are two distinct uses of FOR loops in verilog design, as follows.

1. FOR loop

Used within the always block
Used to evaluate expressions

2. generate FOR loop

Only used outside the always block
Used for instantiating hardware

Understanding FOR Loops

for loops are extremely handy when we want to evaluate multiple assignments within the always block. If we want to write the code for a 4:1 multiplexer, we can easily do so using a either four if blocks or using a case block with 4 cases. However, this approach has very poor scalability. If we wanted to create a 256:1 multiplexer, we would require to write out 256 lines of conditionals and assignmemnts to outline its functionality. Instead, we can use for loops as follows to simplify this code for better readability, and lesser time and effort invested.

integer i;

always @(*)

begin

	for (i = 0, i < 256, i = i + 1) begin
		if (i == sel)
			y = in[i];
		end
	end
  
end

Now, we have easily created a 256:1 multiplexer in just 5 lines of code within the always block; asuuming we have already specified in[255:0] as our input bus. This code can be infinitely scaled up by just replacing the condition i < 256 with the desired specification for our multiplexer.

Example 1:

Below is the file mux_generate.v, found under the verilog_files directory.

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

The above design specifies a 4:1 multiplexer, written using for loops. We have 4 inputs which get assigned to a wire i_int. This is done to allow us to call a specific input easily in the code. Let's simulate this design and view the waveform obtained.

![5-16](https://user-images.githubusercontent.com/86368099/123687660-cd760c80-d86e-11eb-916c-6e3d7b18b57b.png)

As we can see, the design clearly functions as a 4:1 multiplexer. Let us synthesize this design into standard cells using Yosys, and confirm the functionality of our generated netlist using gate level simulation.

![5-17](https://user-images.githubusercontent.com/86368099/123687760-eda5cb80-d86e-11eb-96f3-3d961808085d.png)

![5-18](https://user-images.githubusercontent.com/86368099/123687815-fac2ba80-d86e-11eb-95b3-ab4ec073dfa0.png)

FOR Generate and its Uses

FOR Generate is very useful when we must create multiple instances of the same hardware. For example, if we want to generate 8 AND gates in our design, we would normally do the following.

and u_and0(a.(in1[0]), .b(in2[0]), .c(y[0]));

and u_and1(a.(in1[1]), .b(in2[1]), .c(y[1]));

and u_and2(a.(in1[2]), .b(in2[2]), .c(y[2]));

and u_and3(a.(in1[3]), .b(in2[3]), .c(y[3]));

and u_and4(a.(in1[4]), .b(in2[4]), .c(y[4]));

and u_and5(a.(in1[5]), .b(in2[5]), .c(y[5]));

and u_and6(a.(in1[6]), .b(in2[6]), .c(y[6]));

and u_and7(a.(in1[7]), .b(in2[7]), .c(y[7]));

This is a tedious approach, especially if we want to instantiate a large number of the same hardware. Instead, we can use generate for as follows.

genvar i

generate

	for (i = 0; i < 8; i = i + 1) begin
		and u_and(.a(in1[i]), .b(in2[i]), .c(y[i]));
	end
  
endgenerate

Above, we have replicated 8 instances of a 2 input AND gate using FOR Generate. This can be infinitely scaled up for any number of replications.

Note: We must use the generate for outside the always block. Also, just like generate for, generate if is also possible.

Let us look at an example of a 8 bit Ripple Carry Adder (RCA). An RCA is nothing but Full Adders tied in series, with the carry out of the previous full adder presented as the carry in bit of the next full adder in the chain. Hence, we can make use of generate for to instantiate every full adder in the design, as they are all the same pieces of hardware.

For this example, we make use of the file rca.v which holds the code for the ripple carry adder.

  
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

Here, fa references another verilog design file holding the code for the full adder submodules. This is shown below, from the fa.v file.

module fa (input a , input b , input c, output co , output sum);

	assign {co,sum}  = a + b + c ;
  
endmodule

In the RCA verilog code, we instantiate fa in a loop using generate for outside the always block. However, the first instance of the full adder must be written outside the loop as its carry in input is tied to low, unlike that of every other full adder.

Note: Rules for addition

N and N bit number --> Sum will be N + 1 bits

N and M bit number --> Sum will be max(N, M) + 1 bits

Now, let us simulate this design in Iverilog and view its waveform with GTKWave. As the rca design references the file fa.v, we must specify it in our commands as follows.

iverilog fa.v rca.v tb_rca.v

./a.out

![5-25](https://user-images.githubusercontent.com/86368099/123688532-be438e80-d86f-11eb-9a2f-c34d51902fa3.png)


If we observe the above result, we can see that the design successfully performs 8 bit addition. Similarly, let's synthesize the design and observe the graphical realisation of the netlist in Yosys.

gtkwave tb_rca.v

![5-26](https://user-images.githubusercontent.com/86368099/123688564-c996ba00-d86f-11eb-8921-c68c5decba9e.png)


...

