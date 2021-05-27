# VerilogRTLDesignandSynthesisSky130

Day 1 - Introduction to Verilog RTL Design and Synthesis

1. Simulator is a tool for checking the RTL Design
iverilog is an example.
The output of iverilog Simulator is a .vcd File (value change dump).
Any changes in input will activate the simulator to change output
To view waveforms we use gtkwave

2. What is a .lib?
It is a collection of std cells (Locical Modules) such as AND,OR NOT . These will also contain different flavors of the same Gate

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



