# Verilog_codes_synthesised_using_yosys
These are verilog codes that are synthesized using open source tool yosys


# 1. FULL ADDER (1 BIT) :
   
   
a. Full adder code -
   
module top(a,b, cin, sum, co) ;<br>
input a,b, cin;<br>
output sum, co;<br>
assign sum=a^b^cin;<br>
assign co=(a&b) | (a&c) | (b&c) ;<br>
endmodule<br>
<br>
<br>

b. Generic logic gate netlist using yosys - 

<img width="1470" alt="Screenshot 2024-11-10 at 1 47 14 AM" src="https://github.com/user-attachments/assets/731341ac-6bba-4c05-b617-81865203f821">
This is generic logic gate, we can map this to a technology. 
<br>
<br>

c. Technlogy mapping - 

I used a 45nm pdk and typical process to do technology mapping. 
I created a yosys script instead of giving individual commands. The script is -<br>
 #read design<br>
read_verilog top.v<br>
#elaborate design hierarchy<br>
hierarchy -check -top top<br>
#mapping to internal cell library<br>
synth -top top<br>
#mapping flip-flops to mycells.lib<br>
#dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>
#mapping logic to mycells.lib<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>
#cleanup<br>
clean<br>
#write synthesized design<br>
write_verilog -noattr synth.v<br>

I got the following netlist - 

<img width="1470" alt="Screenshot 2024-11-10 at 3 12 53 AM" src="https://github.com/user-attachments/assets/407682e9-d489-475e-a390-94cd7820347e">

# 2. 4:1 demux

a. Code - <br>

module demux(out0,out1,out2,out3,s1,s0,in); <br>
output out0,out1,out2,out3;<br>
reg out0,out1,out2,out3;<br>
input s1,s0,in;<br>
always @(in or s1 or s0)<br>
case({s1,s0})<br>
2'b00: begin out0=in;out1=1'bz;out2=1'bz;out3=1'bz; end<br>
2'b01: begin out0=1'bz;out1=in;out2=1'bz;out3=1'bz; end<br>
2'b10: begin out0=1'bz;out1=1'bz;out2=in;out3=1'bz; end<br>
2'b11: begin out0=1'bz;out1=1'bz;out2=1'bz;out3=in; end<br>
2'b1x,2'b0x,2'bx1,2'bx0,2'bxx,2'bzx,2'bxz : begin<br>
out0=1'bx;out1=1'bx;out2=1'bx;out3=1'bx; end<br>
2'b1z,2'b0z,2'bz1,2'bz0,2'bzz : begin <br>
out0=1'bz;out1=1'bz;out2=1'bz;out3=1'bz; end<br>
default :$display("Invalid select signal");<br>
endcase<br>
endmodule<br>

<br>
<br>

b. Generic logic gate netlist using yosys - 

<img width="1470" alt="Screenshot 2024-11-10 at 11 57 48 PM" src="https://github.com/user-attachments/assets/1ea80327-96e4-4927-baba-280c1c09dd8a">

<br>
<br>

c. Technlogy mapping - 

I used a 45nm pdk and typical process to do technology mapping. 
I created a tcl script instead of giving individual commands. The script is -<br>

#read modules from Verilog file<br>
 read_verilog top.v<br>

hierarchy -check -top top<br>

#translate processes to netlists<br>
proc<br>

#mapping to internal cell library<br>
 techmap<br>

#mapping flip-flops to NangateOpenCellLibrary_typical.lib<br>
#for eg. always block<br>

dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>

#mapping logic to NangateOpenCellLibrary_typical.lib<br>
#for eg. assign block<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>

 #remove unused cells and wires<br>
clean<br>
#Write the current design to a Verilog file<br>
write_verilog -noattr synth_example.v<br>

<img width="298" alt="Screenshot 2024-11-11 at 12 12 55 AM" src="https://github.com/user-attachments/assets/2b0e954b-a17e-4e9b-8b36-849ed5abaf83">


<br>
<br>

d. Chip area calculation -

Using the command : stat -liberty NangateOpenCellLibrary_typical.lib

<img width="1470" alt="Screenshot 2024-11-11 at 12 08 26 AM" src="https://github.com/user-attachments/assets/b8f6749e-a786-4904-8e58-0c81355ed376">

<br>
<br>

# 3. FULL ADDER (4 BIT) using structural abstraction :

a. Generic logic netlist - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 23 16 AM" src="https://github.com/user-attachments/assets/18489a2b-d32e-4685-9d19-f5c98d74ba7d">

<br>
<br>

# 3. FULL ADDER (4 BIT) using dataflow :

a. Generic logic netlist - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 36 56 AM" src="https://github.com/user-attachments/assets/55c9a973-6845-4b5b-8cec-e18fca41b33b">

<br>
<br>

b. Technlogy mapping - 

<img width="127" alt="Screenshot 2024-11-11 at 12 38 33 AM" src="https://github.com/user-attachments/assets/ef88c6a1-0333-4cf9-ba34-24a19636c6ee">

<br>
<br>

c. Area - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 39 27 AM" src="https://github.com/user-attachments/assets/cb4b5b68-3976-4daa-96a6-7b2cfb423e78">

<br>
<br>








  
