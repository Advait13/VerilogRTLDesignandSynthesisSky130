# VerilogRTLDesignandSynthesisSky130

# Day 1 - Introduction to Verilog RTL Design and Synthesis

1. Simulator is a tool for checking the RTL Design
iverilog is an example.
The output of iverilog Simulator is a .vcd File (value change dump).
Any changes in input will activate the simulator to change output
To view waveforms we use gtkwave

2. What is a .lib?
It is a collection of std cells (Logical Modules) such as AND,OR NOT . These will also contain different flavors of the same Gate

3. Load in Digital Circuit -> Capacitance 
Faster the charging/discharging of capacitor , lesser will be the cell delay.
To charge/discharge a Capacitor fast, we need transistors capable of sourcing more current.
Wider transistors -> Low Delay -> More Area and Power
Narrow transistors -> More delay -> Less Power and Area

4. Labs

A] Yosys

![image](https://user-images.githubusercontent.com/75453542/119771572-c6746c80-be72-11eb-9949-bafe902cd37a.png)

Commands to Read Verilog and Liberty File

![image](https://user-images.githubusercontent.com/75453542/119771616-d7bd7900-be72-11eb-9a3f-c980821ef107.png)

Results of good_mux.v

![image](https://user-images.githubusercontent.com/75453542/119771667-e7d55880-be72-11eb-83dc-56119d084769.png)

Resulting Diagram

![image](https://user-images.githubusercontent.com/75453542/119771691-ef94fd00-be72-11eb-9905-c3edf4b20a27.png)


# Day 2- Timing libs,hierarchical vs flat synthesis and efficient flop coding styles

A] Introduction to timing .libs

Name of Lib (3 characteristics Process(P),Voltage(V) and Temperature(T))
1. SKY130 - 130nm Library 
Variations due to fabrication
2. TT - Typical 
1v80 (1.8)
Variations due to Voltage
3. 025C - Temperature
Variations in Temperature

These 3 characteristics will change the way Silicon Works
Hence, libraries characterized to model these variations
Will the Silicon work in all corner cases?

Example of a lib

![image](https://user-images.githubusercontent.com/75453542/119772849-aa71ca80-be74-11eb-8d77-fbcaa1e44232.png)

Note: All STD Cell Definition will be present here:

Example of a Cell

![image](https://user-images.githubusercontent.com/75453542/119773400-7054f880-be75-11eb-9a18-138b2be6420d.png)

The above Example is a AND-OR Gate (2 Input AND Gate(A1 and A2) and remaing are all OR it with (B1,C1,D1))

![image](https://user-images.githubusercontent.com/75453542/119773980-4f40d780-be76-11eb-96ce-6000b68e5b9a.png)

![image](https://user-images.githubusercontent.com/75453542/119773938-3cc69e00-be76-11eb-9cac-bddc87aba5e2.png)

Note:- PP - Stands for Power Pins

Important Information inside the .lib files
For each cell 
1) Values of Leakage Power for all Gate transistions to a STD CELL (For our example above it is 2^5(inputs) = 32 Combinations)
2) Area Value
3) Power Port Information
4) Describe Each Input 
-Capacitance of each pin 
-Power of each pin
-Delay of each pin

Difference Between multiple flavors of the Same Cell
Note: Looking at the area and power value , you will know which cells are faster(more Area, more Power)

Assessment Day 2 Skill 1

![image](https://user-images.githubusercontent.com/75453542/120015630-65df4f80-bf98-11eb-9adb-f6517393f5db.png)

Q1 and Q2
AND2_4 is a cell with a wider transaitor compared to AND2_2 and AND2_0
Hence
Delay AND2_4 < AND2_2 <AND2_1
Power and Area AND2_4 > AND2_2 > AND2_1

Q4 Highest Leakage Power : NOR2_1

ANS: !A & B

![image](https://user-images.githubusercontent.com/75453542/120015504-3f211900-bf98-11eb-9b1a-9d6f4cd4a33f.png)

Q5 Area of Cell : MUX2_1

![image](https://user-images.githubusercontent.com/75453542/120015414-29135880-bf98-11eb-9b35-1ddbd564a530.png)

Hierarchical vs Flat Synthesis

![image](https://user-images.githubusercontent.com/75453542/120016571-9f648a80-bf99-11eb-804f-e873e40c0f12.png)

![image](https://user-images.githubusercontent.com/75453542/120016721-c6bb5780-bf99-11eb-8e07-93e2ba79bb8a.png)

![image](https://user-images.githubusercontent.com/75453542/120016924-113cd400-bf9a-11eb-8876-3b78c629b614.png)

Diagram of multiple_modules module

![image](https://user-images.githubusercontent.com/75453542/120017086-4a754400-bf9a-11eb-927a-7687097b88d4.png)

Now , writing the Hier Netlist (Hierarchies are Preserved)

![image](https://user-images.githubusercontent.com/75453542/120018303-d20f8280-bf9b-11eb-860a-eebc4bb9f178.png)

Why did OR get coverted in NAND Form in the Netlist?
 NAND vs NOR 
 Stacked NMOS vs Stacked PMOS
 Stacked PMOS : PMOS has poor mobility , need to widen the cell for good locic performance
 
 How to write flat netlist?
 
 ![image](https://user-images.githubusercontent.com/75453542/120019769-b1482c80-bf9d-11eb-8c29-cc5b022cd0d4.png)

 >> write_verilog multiple_modules_flat.v 

![image](https://user-images.githubusercontent.com/75453542/120020486-a17d1800-bf9e-11eb-8639-976a37b5419c.png)


Observations of YOSYS Tool
1. >>yosys to Enter the Synthesis Tool
2. >>read_liberty -lib <lib file> to read all the STD Cells
3. >>read_verilog <module>
4. >>synth -top <module name> Does is do GTCH Map??
5. >>abc -liberty <lib_file> Maps to SKY130, Converts RTL File and what gates it needs to link to  , Generates Netlist
6. >>write_verilog Write Netlist
7. >>flatten Flat Netlist

 Synthesis Result of only sub_module1
 
 ![image](https://user-images.githubusercontent.com/75453542/120021024-5fa0a180-bf9f-11eb-89fe-91a9b98a8293.png)
 
 Why do you want to synthesize sub_modules if we have flat synthesis available?
 Top Module having multiple instantaitions of same module. You can save time by synthesizing these modules only once
 
Various Flop Coding Styles and optimization
 
 Basics: Why Flops?
 Due to Propagation Delay of Combo Cells , Output will glitch until it stabilizes.
 Use Flip-Flop to store values
 
 Flops need to be initialized , otherwise downstream combo inputs might receive Garbage Value
 Asynchronous reset means this doesnt need to wait for next clock edge to reset , instantaneous reset
 Synchronous reset means , it needs to wait for edge of clock, waits for clock not instantaneous
1. Asynchronous Active High Reset with Posedge clock -> always @(posedge clk or async_reset ) if(async_reset) q <= 1'b0 else q<=d
2. Asynchronous Active High Set with Posedge clock -> always @(posedge clk or async_set ) if(async_reset) q <= 1'b0 else q<=d
3. Synchronous Active High Reset with Posedge clock -> always @(posedge clk  ) if(async_reset) q <= 1'b0 else q<=d
4. Asyncronous Active High Reset , active high synchronouus reset with Posedge clock -> always @(posedge clk or async_reset ) if(async_reset) q <= 1'b0 else if(sync_reset) q<= 1'b0; else q<=d

Need to worry about Flops having both aynsc Set and Reset as it might cause race consitions

Lab flop synthesis simulations 

Async Reset D-FF

![image](https://user-images.githubusercontent.com/75453542/120026247-a80f8d80-bfa6-11eb-9a3f-5a7f405357cc.png)

Waveform

![image](https://user-images.githubusercontent.com/75453542/120026449-f1f87380-bfa6-11eb-9ada-7c53cb1e4e3f.png)

Async Set D-FF

![image](https://user-images.githubusercontent.com/75453542/120026870-8fec3e00-bfa7-11eb-83ef-ccf7e444391b.png)

Waveform

As seen below at "alpha" Q follows D

![image](https://user-images.githubusercontent.com/75453542/120027544-59fb8980-bfa8-11eb-82af-b41962e50f06.png)

Async Reset Sync Reset D-FF

![image](https://user-images.githubusercontent.com/75453542/120028586-b7dca100-bfa9-11eb-9deb-4416dd651ac9.png)

Waveform

As seen below Async Reset will make Q is 0 and Q is 1 after pos clock edge because d is high and when asyn reset is low but sync reset is high Q is low after clock edge

![image](https://user-images.githubusercontent.com/75453542/120028749-f4a89800-bfa9-11eb-80de-2de189382c6c.png)

Synthesis of Async Reset D-FF

![image](https://user-images.githubusercontent.com/75453542/120029341-d3947700-bfaa-11eb-9a5e-9d20e2518287.png)

>>synth -top dff_asyncreset

![image](https://user-images.githubusercontent.com/75453542/120029415-e9a23780-bfaa-11eb-9198-3d87b36ccc66.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120029501-0c345080-bfab-11eb-802a-d3a4e19662ae.png)
 
 Executing Synthesis Step...
 
 ![image](https://user-images.githubusercontent.com/75453542/120029619-31c15a00-bfab-11eb-8c22-0aaae8e5ff4e.png)

 Diagram
 
![image](https://user-images.githubusercontent.com/75453542/120029717-53224600-bfab-11eb-90ae-0b157f2e0bba.png)





