# RISC-V-Reference-SoC-Tapeout-Program-week3
## Synthesis and GLS of BabySoC
### Labs
### Load the verilog Files
```
yosys
read_verilog /home/souhardyab/VLSI/VSDBabySoC/src/module/vsdbabysoc.v
read_verilog -I /home/souhardyab/VLSI/VSDBabySoC/src/include /home/souhardyab/VLSI/VSDBabySoC/src/module/rvmyth.v
read_verilog -I /home/souhardyab/VLSI/VSDBabySoC/src/include /home/souhardyab/VLSI/VSDBabySoC/src/module/clk_gate.v
```
### Load the Liberty Files
```
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/avsdpll.lib
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/avsddac.lib
read_liberty -lib /home/souhardyab/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
### Run Synthesis
```
synth -top vsdbabysoc
```
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
### Statistics
```
stat
```
### Write Netlist
```
write_verilog -noattr /home/souhardyab/VLSI/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
```





## Static Timing Analysis
