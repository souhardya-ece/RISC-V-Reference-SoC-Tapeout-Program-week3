# RISC-V-Reference-SoC-Tapeout-Program-week3
## Synthesis and GLS of BabySoC
### Labs (Synthesis)
### Load the verilog Files
```
yosys
read_verilog /home/souhardyab/VLSI/VSDBabySoC/src/module/vsdbabysoc.v
read_verilog -I /home/souhardyab/VLSI/VSDBabySoC/src/include /home/souhardyab/VLSI/VSDBabySoC/src/module/rvmyth.v
read_verilog -I /home/souhardyab/VLSI/VSDBabySoC/src/include /home/souhardyab/VLSI/VSDBabySoC/src/module/clk_gate.v
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Yosys.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Read_vsd.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/read_rv.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/read_clk.png)
### Load the Liberty Files
```
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/avsdpll.lib
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/avsddac.lib
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Lib.png)
### Run Synthesis
```
synth -top vsdbabysoc
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Top.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Remove.png)
### Map D f/f to standard cell
```
dfflibmap -liberty /home/souhardyab/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
### Optimization
```
opt
abc -liberty /home/souhardyab/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
### Clean up
```
flatten
setundef -zero
clean -purge
rename -enumerate
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Flat.png)
### Statistics
```
stat
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Stat.png)
### Write Netlist
```
write_verilog -noattr /home/souhardyab/VLSI/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
show
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Write.png)
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Synth.png)
### GLS of BabySoC
```
cd VLSI
cd VSDBabySoC 
iverilog -o /home/souhardy
ab/VLSI/VSDBabySoC/output/post_synth_sim/post_synth_sim.out -DPOST_SYN
TH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 -I /home/souhardyab/VLSI/VSDBabySo
C/src/include -I /home/souhardyab/VLSI/VSDBabySoC/src/module /home/sou
hardyab/VLSI/VSDBabySoC/src/module/testbench.v
cd output
cd post_synth_sim
./post_synth_sim.out
gtkwave post_synth_sim.vcd
```
### Output
![image alt](https://github.com/souhardya-ece/RISC-V-Reference-SoC-Tapeout-Program-week3/blob/main/Images/Post_synth.png)
## Static Timing Analysis
### Introduction
Static timimg analysis consists of three parts those are checks,constraints,library.
### Checks
Here we can consider timing path (start point:-flop clk pin/input pin,end point:-flop d pin/output pin)
Arrival time is the required for a singnal from go to start point to end point.
Required  time is the expected time for a singal to arrive at the end point.Suppose a signal reached at the end point between 1 to 3 ns => 1< arrival time(=3.1) <3 
Slack is the difference between the arrival time and the expected time .
Min slack =2.1(hold time) max slack = -.1 (setup time).
### Types os setup and hold analysis
1. Reg2reg:- Where the input is launch flop(clk) and output is capture flop(d pin).
2. In2reg:- Where the input is in pin and output is register d pin.
3. Reg2out:- Where the input is register q pin and the output is out pin
4. In2out:- Where the the input is in pin and output is out pin
Clock Gating:- Sometimes the clk does not always go into buffer . With some logic it goes into the register. So the power is reduce rapidly. 
Recovery and removal:- Sometimes there are some async pin in the register so there are is a valid timing path get created.


