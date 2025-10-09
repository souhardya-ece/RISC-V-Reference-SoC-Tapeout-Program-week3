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
5.Clock Gating:- Sometimes the clk does not always go into buffer . With some logic it goes into the register. So the power is reduce rapidly. 
6.Recovery and removal:- Sometimes there are some async pin in the register so there are is a valid timing path get created.
7.Data to data:- it gots from clk to async comb logic
8.Latch(borrow/given):-flop to latch or vise versa.
### Slew/Transition Analysis:-
1. Data:- We check the data path and measure the max and min.
2. Clk:-Here we can also do the same for the clk.
### Load Analysis
1. Fanout:- Max similar kind of thing that is connected to the output.
2. Capacitance:- There are max and min value at the load of the capacitance.
### Clock Analysis
1. Skew :- latency difference bw different path(from clk to the reg i/p)
2. Pulse width:- clk that is same goes to the each and every clk (in bw there are some paracitic)
### Reg2Reg anlysis
First we consider a comb ckt input ports have some required arrival time(delay) and in between the pin and the gate there is some wire which have some delay gates also consists of delay note that input and the output pin does not have its won delay. so we convert the whole ckt into a DAC directed ascyclic graph(for more accurate it could be pin node convention => each node have its i/p output slack ) and we calculate the arrival time(Actual arrival time:- Time at any node where is the transition of first rise clk edge) of the each node of that graph now there might be the case where two arrival time(cosider worst case) of a node (min/max).
Required arrival time:- time at any node where the transition of within the clk.Let the output pin have some rat so we sub the delay and calculate the each node rat . some node have some two rat consider th best case.
Slack comp:- Rat-aat if it is +ve then the design meet the expectation.calculate the slack of each node. So tune the slack we cosider the worst case condition this is graph based anlysis.on the other hand real path  would be path based analysis.
The delay or the time from clk to d pin should be less than the time period of the clk. If we consider cts the del1-del2=skew








