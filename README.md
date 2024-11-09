# Verilog_codes_synthesised_using_yosys
These are verilog codes that are synthesized using open source tool yosys


# 1. FULL ADDER (1 BIT) :
   
   
a. Full adder code -
   
module top(a,b, cin, sum, co) ;
input a,b, cin;
output sum, co;
assign sum=a^b^cin;
assign co=(a&b) | (a&c) | (b&c) ;
endmodule

b. Generic logic gate netlist using yosys - 

<img width="1470" alt="Screenshot 2024-11-10 at 1 47 14 AM" src="https://github.com/user-attachments/assets/731341ac-6bba-4c05-b617-81865203f821">
This is generic logic gate, we can map this to a technology. 
&nbsp;&nbsp;&nbsp;&nbsp;

c. Technlogy mapping - 

I used a 45nm pdk and typical process to do technology mapping. 
I created a yosys script instead of giving individual commands. The script is -
 #read design
read_verilog top.v
#elaborate design hierarchy
hierarchy -check -top top
#mapping to internal cell library
synth -top top
#mapping flip-flops to mycells.lib
#dfflibmap -liberty NangateOpenCellLibrary_typical.lib
#mapping logic to mycells.lib
abc -liberty NangateOpenCellLibrary_typical.lib
#cleanup
clean
#write synthesized design
write_verilog -noattr synth.v

I got the following netlist - 

<img width="1470" alt="Screenshot 2024-11-10 at 3 12 53 AM" src="https://github.com/user-attachments/assets/407682e9-d489-475e-a390-94cd7820347e">






  
