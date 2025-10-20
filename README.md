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

### CKT Analysis

First we consider a comb ckt input ports have some required arrival time(delay) and in between the pin and the gate there is some wire which have some delay gates also consists of delay note that input and the output pin does not have its won delay. so we convert the whole ckt into a DAC directed ascyclic graph(for more accurate it could be pin node convention => each node have its i/p output slack ) and we calculate the arrival time(Actual arrival time:- Time at any node where is the transition of first rise clk edge) of the each node of that graph now there might be the case where two arrival time(cosider worst case) of a node (min/max).

Required arrival time:- time at any node where the transition of within the clk.Let the output pin have some rat so we sub the delay and calculate the each node rat . some node have some two rat consider th best case.

Slack comp:- Rat-aat if it is +ve then the design meet the expectation.calculate the slack of each node. So tune the slack we cosider the worst case condition this is graph based anlysis.on the other hand real path  would be path based analysis.

The delay or the time from clk to d pin should be less than the time period of the clk. If we consider cts the del1-del2=skew

Negative latch(active when negative latch) and Positive latch(active when positive level) both combine to make a filp flop. At negative level the amount of time d goes to Qm  is called libery setup time(before rising edge).At positive level the data is goes from inv5 o/p to q. Hold time is the time d is valid after positive edge.

When we consider a clk and its inv version it have some distorted value .Rising and falling shifted region is known as jitter through eye diagram.

So we get the equation in setup analysis  theta+del1(arrival time=>push out)< T+del2-s-su(required time=> clk pull in) => Convert this into txtual representation. Slack =r-a =+ve or 0

so we get the equation in hold analysis  theta+del1(arrival time=> clk pull in)> H+del2+HU (required time=> clk push out) => Convert this into txtual representation. Slack =a-r =+ve or 0

OCV(on chip variation):-Etching is the fabrication process that diectly effect the delay . It effects on W/L ratio which impact in drain current.

Oxide Thickness:- It can effect the delay like cox=eox/tox tox changes so that it will effect to the drain current.

An cmos inv is a simple rc n/w so the delay will depend upon r that is the function of drain current.

So we increase and decrease each delay by +-20%(ocv) so that in the setup time the slack may be worse.but when we consider the extra pessimism the slck become positive. and the same case for hold analysis.

## Generate Timing Graphs with OpenSTA 
To generate the timing grapha and do  the PVT corner analysis for post synthesis timing of the RISC-V .First of all Clone the open sta git repository
```
git clone https://github.com/parallaxsw/OpenSTA.git
cd OpenSTA
```
Then inside the file location(..\\home\souhardyab\VLSI\VSDBabySoC\OpenSTA\examples\timing_libs) put the all required files which is in the file repo and the sky 130 lib files which is in this location (..\\home\souhardyab\VLSI\VSDBabySoC\skywater-pdk-libs-sky130_fd_sc_hd\timing) then rune 'sta'
Lets take an example of an lib file sky130_fd_sc_hd__tt_025C_1v80.lib and how to generate the worst setup time ,worst  hold time , WNS , TNS. 
```
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty avsdpll.lib
read_liberty asvddac.lib
read_verilog vsdbabysoc.synth.v
link_design vsdbabysoc
current design
read_sdc vsdbabysoc_synthesis.sdc
report_checks -path_delay min_max
```
### Output
![image alt]()
![image alt]()
![image alt]()

Likewise do all the sky 130 lib file with PVT corner and get the worst setup time ,worst  hold time , WNS , TNS and generate the graph out of these data
![image alt]()
### Graphs
![image alt]()
![image alt]()
![image alt]()
![image alt]()










