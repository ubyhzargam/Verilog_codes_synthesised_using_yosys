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

b. Internal model representation - 
This is known as syntax tree. This is the first stage of RTL synthesis known as parsing. 

<img width="1470" alt="Screenshot 2024-11-10 at 1 47 14 AM" src="https://github.com/user-attachments/assets/731341ac-6bba-4c05-b617-81865203f821">
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

b. Internal model representation - 

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

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 23 16 AM" src="https://github.com/user-attachments/assets/18489a2b-d32e-4685-9d19-f5c98d74ba7d">

<br>
<br>

# 4. FULL ADDER (4 BIT) using dataflow :

a. Internal model representation - 

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

# 5. Edge detector :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 43 47 AM" src="https://github.com/user-attachments/assets/5895f55d-cce2-4256-9618-82a9ad2ba610">

<br>
<br>

b. Technology mapping - 

<img width="88" alt="Screenshot 2024-11-11 at 12 46 24 AM" src="https://github.com/user-attachments/assets/f242bdb9-9634-4170-89fa-49cced2bb01d">

<br>
<br>

c. Area - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 51 37 AM" src="https://github.com/user-attachments/assets/3a8ce312-39d5-4701-a22f-7e9afa07651a">

<br>
<br>

# 6. BCD counter :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 54 23 AM" src="https://github.com/user-attachments/assets/e7001db8-bc9c-4c30-bdbb-c47fc49965f7">

<br>
<br?

b. Technology mapping - 

<img width="159" alt="Screenshot 2024-11-11 at 12 55 54 AM" src="https://github.com/user-attachments/assets/728cbcad-f48f-4d77-96ed-44e38fb1c3b8">

<br>
<br>

c. Area - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 56 53 AM" src="https://github.com/user-attachments/assets/a047ac17-8f1c-448e-a26e-e62d8f23f645">

<br>
<br>

# 7. Carry look ahead adder (4 bit) :

a. Internal model representation -

<img width="1470" alt="Screenshot 2024-11-11 at 1 00 15 AM" src="https://github.com/user-attachments/assets/5167be3c-912b-4a95-bc35-0afae0f4bab0">

<br>
<br>

b. Technology mapping - 

<img width="165" alt="Screenshot 2024-11-11 at 1 01 34 AM" src="https://github.com/user-attachments/assets/50821000-d100-4788-aee5-68a3f59a1c3a">

<br>
<br>

c. Area - 

<img width="1470" alt="Screenshot 2024-11-11 at 1 02 19 AM" src="https://github.com/user-attachments/assets/cd73355c-a13f-4d37-904e-34cd1ec71e5f">

<br>
<br>

# 8 Overflow detector :

a. Generic logic gate -<br>
I used different set of commands this time. Instead of generating a internal model, I generated a generic logic gate netlist using the following command - 
read_verilog top.v; <br>synth -top top;<br> write_verilog top_netlist.v<br>

<img width="1405" alt="Screenshot 2024-11-19 at 1 06 44 AM" src="https://github.com/user-attachments/assets/0cbb2349-f7b1-4751-9ae1-4e84c5a8f895">

<br>
<br>

b. Generic gate logic optimization - <br>
read_verilog mydesign.v;<br> synth; <br> opt; <br> clean; <br> write_verilog mydesign_optimized.v<br> 
<img width="1470" alt="Screenshot 2024-11-19 at 1 22 45 AM" src="https://github.com/user-attachments/assets/b9f24319-cde3-4d27-be94-7617903a24d6">


c. Technology mapping - <br>
I also updated the technology mapping commands to get a better view of the gates and mapping - <br>
read_liberty -lib NangateOpenCellLibrary_typical.lib<br>
read_verilog top.v<br>
synth -top top<br>
dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>
write_verilog -noattr top_mapped.v<br>

<img width="1470" alt="Screenshot 2024-11-19 at 1 13 18 AM" src="https://github.com/user-attachments/assets/c3a4e68c-74c8-4dbc-8b2d-c9de89eff335"><br>

<br>
<br>

d. Area - <br>
<img width="431" alt="Screenshot 2024-11-19 at 12 56 17 AM" src="https://github.com/user-attachments/assets/ae0092c4-b795-4190-92ef-eaa5b3f77157">


<br>
<br>

d. Logic Optimization - <br>
Commands opt was given<br>
Also resource sharing was made possible using the command share -aggressive<br>
<img width="517" alt="Screenshot 2024-11-19 at 12 59 26 AM" src="https://github.com/user-attachments/assets/7b4d2fbe-9660-466c-88e7-f08abf28544f"><br>
<img width="403" alt="Screenshot 2024-11-19 at 12 59 53 AM" src="https://github.com/user-attachments/assets/c5991bfe-70c4-4090-9800-c8a6f37a2a40"><br>

SAT word used above stands for Satisfiability solvers. SAT solvers and ROBDD are used extensively for represetation of Boolean functions. They are canonical and compact and make optimization and verification of circuits easier. <br>

<img width="45" alt="Screenshot 2024-11-19 at 12 58 04 AM" src="https://github.com/user-attachments/assets/043013c3-c8df-49c3-a9e3-11ad331ff8a4">

# 9 FSM to detect more than one 1s in the last 3 samples :

a. Internal model representation - <br>
<img width="492" alt="Screenshot 2024-11-21 at 9 21 12 PM" src="https://github.com/user-attachments/assets/6d7dd888-da26-4e37-8d08-db01b92c01e1">

<br>
<br>
b. Generic logic gate -<br>

<img width="1470" alt="Screenshot 2024-11-21 at 9 22 56 PM" src="https://github.com/user-attachments/assets/f69167f7-08a3-4f0b-a330-73b0c9c2ddcb">
<br>
<br>

c. Optimized Generic logic gate -<br>
<img width="1470" alt="Screenshot 2024-11-21 at 9 25 01 PM" src="https://github.com/user-attachments/assets/3443c5bb-d239-45ae-82ef-3d2798c089af">

<br>
<br>

d. Technology mapped optimised netlist - <br>
<img width="1470" alt="Screenshot 2024-11-21 at 9 46 27 PM" src="https://github.com/user-attachments/assets/8a18945b-6584-47c1-97c6-638385e6309e">

<br>
<br>

e. Area - <br>
<img width="462" alt="Screenshot 2024-11-21 at 9 49 42 PM" src="https://github.com/user-attachments/assets/dd2f1b8f-464d-43dd-8239-39facaef69cb">

<br><br>  
