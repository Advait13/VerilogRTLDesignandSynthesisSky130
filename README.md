# Verilog RTL Design and Synthesis Sky130

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

 Diagram (Tool Inserted an INV because Tool used Active Low FF hence requiring Inverter as Reset is ACTIVE HIGH)
 
![image](https://user-images.githubusercontent.com/75453542/120029717-53224600-bfab-11eb-90ae-0b157f2e0bba.png)
 
 Synthesis of Sync Reset D-FF


 >>synth-top dff_syncres
 
 ![image](https://user-images.githubusercontent.com/75453542/120036835-91bcfe00-bfb5-11eb-9857-3261271f12a4.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120036968-bca75200-bfb5-11eb-973a-7bda6852a6b5.png)

 Executing Synthesis Step...
 
 ![image](https://user-images.githubusercontent.com/75453542/120037016-d052b880-bfb5-11eb-8aed-7995ac09e8a1.png)

 Diagram 
 
 ![image](https://user-images.githubusercontent.com/75453542/120037087-ebbdc380-bfb5-11eb-87f9-4312e0ff149a.png)

 Interesting Optimizations
 
 Utilizing Appending/Shift to Execute Multiply by 2^x Operation (Redistributing Wires (No H/W Built))

 Synthesizing MULT2
 
 ![image](https://user-images.githubusercontent.com/75453542/120038719-8e774180-bfb8-11eb-97a5-3ca77d570476.png)
 
 Cell Profile:
 
 ![image](https://user-images.githubusercontent.com/75453542/120038801-abac1000-bfb8-11eb-97eb-994f09b5fa53.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120038893-d1391980-bfb8-11eb-9391-d8727fe2767b.png)
 
 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120038964-eada6100-bfb8-11eb-8e92-b0519c97a245.png)
 
 Netlist:
 
 ![image](https://user-images.githubusercontent.com/75453542/120039550-f8dcb180-bfb9-11eb-9d21-a8bbca69194b.png)


 Synthesizing MULT9

Cell Profile:
 
 ![image](https://user-images.githubusercontent.com/75453542/120039714-3a6d5c80-bfba-11eb-8177-4083ce0c0f68.png)

Linking not required to STD CELLS as seen below
 
 ![image](https://user-images.githubusercontent.com/75453542/120039775-4fe28680-bfba-11eb-84f6-9dd9f224d108.png)

Diagram:

 ![image](https://user-images.githubusercontent.com/75453542/120039958-9df78a00-bfba-11eb-9c7c-204e426d18a3.png)

Netlist:
 
 ![image](https://user-images.githubusercontent.com/75453542/120040063-c97a7480-bfba-11eb-90bb-45528ad2e8ce.png)

Optimizations are important to conserve hardware and cost of chip manufacturing

# Day 3- Combinational and sequential optimizations
 
 Introduction to optimisations
 
Combinational Logic Optimisation
 
 1. Constant Propagation
 2. Boolean Logic Optimisation (K-Map, Quine McKluskey Algorithms)
 
 Sequential Logic Optimisation
 
 1. Sequential Costatnt Propagation
 2. State Optimisation
 Optimization of unused States
 3. Retiming
 Distributing combo logic in bw 2 Flops more effectively to reduce bottleneck path thereby increasing clock frequency
 4. Sequential Logic Cloning (Floorplan Aware Synthesis)
 If B and C flops are far away  from A , if there is a large positive slack , then A can be cloned
 
 Labs
 
 opt_check (mux with sel=a , one input=b and second input = 1'b0)
 
 ![image](https://user-images.githubusercontent.com/75453542/120043216-6ab7f980-bfc0-11eb-9883-f6f96a040bfa.png)
 
 opt_check module Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120043263-7dcac980-bfc0-11eb-8672-5e5635cd88fd.png)
 
 Optimizing Command (Unused Cells Optimizations are Done) 

 ![image](https://user-images.githubusercontent.com/75453542/120043331-9c30c500-bfc0-11eb-9eb6-b1ad9df50a1a.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120043635-2c6f0a00-bfc1-11eb-841a-ffb57cb5c9ef.png)

opt_check2 (mux with sel=a , one input=b and second input = 1'b1)
 
 opt_check2 module Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120043815-87086600-bfc1-11eb-9656-1ed5f9a723df.png)
 
 Optimizing...
 
 ![image](https://user-images.githubusercontent.com/75453542/120043872-9daebd00-bfc1-11eb-87c0-683e00bf0f8e.png)
 
 Diagram below shows (NAND representatioon of OR gate)

 ![image](https://user-images.githubusercontent.com/75453542/120043912-ad2e0600-bfc1-11eb-9eab-0f7d29109f48.png)

opt_check3 (assign y = a? (c?b:0):0 )
 
 ![image](https://user-images.githubusercontent.com/75453542/120044851-8375de80-bfc3-11eb-9f83-8d98589b8470.png)

 opt_check3 module Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120044884-98eb0880-bfc3-11eb-8939-458326be0488.png)

 Optimizing...
 
 ![image](https://user-images.githubusercontent.com/75453542/120045873-d6e92c00-bfc5-11eb-8973-9566b6b689cb.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120045920-e8cacf00-bfc5-11eb-8d9f-d3d4c285d4b9.png)

opt_check4 (assign y= a?(b?(a&c):c):(!c))
 
 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046326-c38a9080-bfc6-11eb-9dc7-5d40bf7c3ddc.png)
 
 Optimizing...
 
  ![image](https://user-images.githubusercontent.com/75453542/120045873-d6e92c00-bfc5-11eb-8973-9566b6b689cb.png)
 
 Diagram (A XNOR C Operation):

 ![image](https://user-images.githubusercontent.com/75453542/120046422-f765b600-bfc6-11eb-82d2-313c68c0b970.png)

 multiple_module_opt.v
 
 ![image](https://user-images.githubusercontent.com/75453542/120046178-7dcdc800-bfc6-11eb-8722-b862a95fc7ef.png)
 
 Cell Report: 
 
 ![image](https://user-images.githubusercontent.com/75453542/120046662-84107400-bfc7-11eb-9d36-39d884a68f3e.png)

 Flatten:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046699-95f21700-bfc7-11eb-844c-369acf93927e.png)

 Optimize:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046716-a3a79c80-bfc7-11eb-8604-a91513541d03.png)
 
 Link:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046760-ba4df380-bfc7-11eb-827a-6b25cc081c81.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120047201-e322b880-bfc8-11eb-9b94-789e3dd3ede2.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046804-d2257780-bfc7-11eb-98b1-c4814c38618c.png)

 Netlist:
 
 ![image](https://user-images.githubusercontent.com/75453542/120046875-039e4300-bfc8-11eb-82a7-bb9a317b4709.png)

 
 multiple_module_opt2.v
 
 ![image](https://user-images.githubusercontent.com/75453542/120046208-8e7e3e00-bfc6-11eb-9f58-deb0f6cbacfb.png)

 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047809-6395e900-bfca-11eb-945f-8a0a90b5d44c.png)

 Flatten:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047863-81fbe480-bfca-11eb-912a-070d3fa59e27.png)
 
 Optimize:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047882-8e803d00-bfca-11eb-8d00-b53e0aeb9529.png)

 Link:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047925-ad7ecf00-bfca-11eb-8e2d-d942a9e8ab4f.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047940-bb345480-bfca-11eb-8d5a-6fa0b22c31cd.png)

 Netlist:
 
 ![image](https://user-images.githubusercontent.com/75453542/120047996-d69f5f80-bfca-11eb-99dd-ec3d3e99742c.png)

 ![image](https://user-images.githubusercontent.com/75453542/120048027-eae35c80-bfca-11eb-87d3-8781b9b672d2.png)

 
 Sequential Optimization Labs 
 
 dff_const1 module

 ![image](https://user-images.githubusercontent.com/75453542/120049720-b1f9b680-bfcf-11eb-9a9e-1f7979b8c8f2.png)

 Event of Q = 1 , flop will be inferred
 
 Simulation Results
 
 ![image](https://user-images.githubusercontent.com/75453542/120049840-25032d00-bfd0-11eb-8002-5af5c671df36.png)

 Q waits for clock edge
 
 ![image](https://user-images.githubusercontent.com/75453542/120049879-49f7a000-bfd0-11eb-8fa2-5cc29200d9a1.png)
 
 Synthesis Results

 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120050243-94c5e780-bfd1-11eb-86f6-e60c2cddd1c9.png)
 
 DFF-Libmap
 
 ![image](https://user-images.githubusercontent.com/75453542/120050687-a491fb80-bfd2-11eb-9dee-b26bbb6793d2.png)

 Optimzation + Linking
 
 ![image](https://user-images.githubusercontent.com/75453542/120050236-88da2580-bfd1-11eb-886f-1b7263ff3d2a.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120050638-7f9d8880-bfd2-11eb-892b-a171de04c524.png)

 dff_const2 module
 
 ![image](https://user-images.githubusercontent.com/75453542/120049741-c50c8680-bfcf-11eb-8eca-2d19b73a4993.png)
 
 All cases Q = 1, constant
 
 ![image](https://user-images.githubusercontent.com/75453542/120049943-7f03f280-bfd0-11eb-9fa0-7eafe8793239.png)

 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120050318-d3f43880-bfd1-11eb-9744-e7dafdf67fde.png)

 Optimization + Linking:
 
 ![image](https://user-images.githubusercontent.com/75453542/120050344-f38b6100-bfd1-11eb-9b07-32bc562c6084.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120050357-fede8c80-bfd1-11eb-8f19-2a7b0a7a4aef.png)

 dff_const3 module
 
 Simulation:
 
 ![image](https://user-images.githubusercontent.com/75453542/120051159-5ed63280-bfd4-11eb-98d2-40dce7b3ed1a.png)

 ![image](https://user-images.githubusercontent.com/75453542/120051138-4bc36280-bfd4-11eb-8c08-894b5825e6fd.png)
 
 Synthesis:
 
 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120051199-8d540d80-bfd4-11eb-9754-1fd17173cfc6.png)

 DFF Map:
 
 ![image](https://user-images.githubusercontent.com/75453542/120051214-9ba22980-bfd4-11eb-8e5f-4c989b2ed0bb.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120051265-cbe9c800-bfd4-11eb-9549-43b7c328612f.png)

dff_const4 module
 
 ![image](https://user-images.githubusercontent.com/75453542/120078374-1f572700-c064-11eb-8ef5-804bea4d4b4c.png)
 
 Simulation Results
 
 ![image](https://user-images.githubusercontent.com/75453542/120078589-00a56000-c065-11eb-8e34-6a45025616a9.png)

 ![image](https://user-images.githubusercontent.com/75453542/120078605-0ef37c00-c065-11eb-9500-b7e130535310.png)

 Synthesis Results
 
 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078698-9d67fd80-c065-11eb-8555-a8bafb40afef.png)

Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078718-b670ae80-c065-11eb-9539-487ebec48505.png)

 dff_const5 module
 
 ![image](https://user-images.githubusercontent.com/75453542/120078383-30079d00-c064-11eb-81b5-651ff7ae4d9f.png)
 
 Simulation Results
 
 ![image](https://user-images.githubusercontent.com/75453542/120078665-685bab00-c065-11eb-9007-da86a80fa023.png)

 ![image](https://user-images.githubusercontent.com/75453542/120078681-79a4b780-c065-11eb-8c96-93995f265891.png)

 Synthesis Results
 
 Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078746-d99b5e00-c065-11eb-93dc-f9b7d031309c.png)
 
 DFF Map:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078766-ed46c480-c065-11eb-8f6d-e339c2a07100.png)

Linking:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078801-03548500-c066-11eb-891e-6f442920c58b.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120078813-11a2a100-c066-11eb-9a0e-abb7e3650755.png)

Sequential Optimizations for unused outputs
 
 counter_opt.v module
 
 ![image](https://user-images.githubusercontent.com/75453542/120081097-91ce0400-c070-11eb-858b-0ade135fc16b.png)

Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120081165-ea050600-c070-11eb-83e0-dfad5db035a7.png)

 Diagram:
 
![image](https://user-images.githubusercontent.com/75453542/120081200-36e8dc80-c071-11eb-9235-d0d743f6db8a.png)

 Case : Using all 3 bits of count register
 
  counter_opt.v module
 
 ![image](https://user-images.githubusercontent.com/75453542/120081400-546a7600-c072-11eb-9dc3-8b868a5e5255.png)

 ![image](https://user-images.githubusercontent.com/75453542/120081417-6ba96380-c072-11eb-8b25-ddcdf0494fe3.png)

Cell Report:
 
 ![image](https://user-images.githubusercontent.com/75453542/120081428-79f77f80-c072-11eb-8034-165179e9bbff.png)

 Diagram:
 
 ![image](https://user-images.githubusercontent.com/75453542/120081442-93003080-c072-11eb-81eb-10f32c51a42f.png)

 
Case: checking if count = 3'b101
 
 ![image](https://user-images.githubusercontent.com/75453542/120081587-4701bb80-c073-11eb-8b40-289284220ea3.png)
 
 ![image](https://user-images.githubusercontent.com/75453542/120081517-ea9e9c00-c072-11eb-892e-0e1e87bffccd.png)
 
 Case : count + 4
 
 Here, q output always a zero
 
 ![image](https://user-images.githubusercontent.com/75453542/120081615-6567b700-c073-11eb-8f9c-2cbf23da5f67.png)

 ![image](https://user-images.githubusercontent.com/75453542/120081646-816b5880-c073-11eb-9d89-97e5fce6ba14.png)


 
