---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 8 RTL设计与测试/"}
---


# 8. RTL 设计与测试
## 8.1 时序乘法器
#### 8.1.1 移位相加时序乘法器模块组成
-  输入 A 和 B 为 8 比特双向 I/O 接口，输出结果为 16 比特，分为两个字节，一次输出一个字节；
- `start` 脉冲启动乘法运算，在其上升沿，操作数 `A` 送入 `databus`，下一个时钟有效时操作数 B 送入 `databus`；
- 当 `Isb_out` 为高电平时，输出结果的低字节；
- 当 `Msb_out` 为高电平时，输出结果的高字节；
- 当结果的高低字节都输出之后，`done` 的值变为 1，乘法器可以进行下一次运算；
- 复用的双向总线 `databus` 可以减少乘法器的输入输出引脚；
![Lecture 8 RTL设计与测试-1746272772658.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272772658.png)
### 8.1.2 移位相加实现乘法的过程
- 由操作数 `A` 中第 `i` 位的值决定操作数 B 是否与运算的部分和相加，完成后新结果右移一位；
	- 如果 `A` 中第 `i` 位是 1，则把 `B` 的值与部分和相加，并将新的结果右移一位；
	- 如果 `A` 的第 `i` 位是 0，则直接将部分和右移一位；
![Lecture 8 RTL设计与测试-1746272775135.png|手工进行二进制乘法运算](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272775135.png)
实际硬件计算时，若 `A` 的值为 0，则直接将上个部分和移位；若 `A` 的值为 1，则将部分和与 B 相加，再移位。
4 比特运算：
![Lecture 8 RTL设计与测试-1746272779118.png|基于硬件的乘法过程](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272779118.png)

### 8.1.3 时序乘法器的设计
#### 8.1.3.1 控制与数据的分离
- 乘法器分为两个部分
	- 数据部分：寄存器、逻辑单元、互联总线
	- 控制器：作为状态机，发出控制信号，定时控制流入数据寄存器的数据
![Lecture 8 RTL设计与测试-1746272782906.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272782906.png)
#### 8.1.3.2 乘法器的数据通路
- `P` 和 `B` 是 8 比特的寄存器的输出，`A` 是 8 比特的移位寄存器的输出，由 `always` 语句实现；数据通路的其它部分由加法器、多路复用器和两个三态缓冲器组成，由 `assign` 语句实现；
- 与输入 `A` 和 `B` 相连的总线 `databus` 将乘数和被乘数载入相应的寄存器；
- 双向总线通过三态缓冲器由 `A` 和 `P` 的输出驱动，计算出乘法结果后，经由三态缓冲器输出；
- 如果进行了加法运算，`B` 和 `P` 相加后的输出构成了 `co，sum`，`sum` 放入 `P`；否则，将 `P` 送入 `ShiftAdd` 进行移位，得到的值再送回 `P`；由控制信号 `sel_sum` 选择将 `sum` 或 `P` 送到乘法器输出；
- 下图所示与门根据 `sel_sum` 的值输出加法器结果或者 0，输出值连在乘法器输出结果的高位，构成 9 比特向量，即 `P+B`；将它的最低位送个存放 A 的移位寄存器，其余值送给寄存器 `P`；
![Lecture 8 RTL设计与测试-1746356639902.png|乘法器模块结构图（与Verilog代码相对应）](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746356639902.png)
#### 8.1.3.3 数据通路的描述
```Verilog
module datapath (input clk,clr_P,load_P,load_B,msb_out,lsb_out,
sel_sum,load_A,shift_A,inout [7:0] data, output A0);
	wire [7:0] sum, ShiftAdd;
	reg [7:0] A,B,P;
	wire co;
	always @ (posedge clk) if (load_B) B <= data;
	always @ (posedge clk) if (load_P) P <= {co & sel_sum, ShiftAdd[7:1]};
	
	assign {co,sum} = P + B;
	always @ (posedge clk)
		case ({load_A,shift_A})
			2'b01 : A <= {ShiftAdd[0], A[7:1]};
			2'b10 : A <= data;
			default : A <= A;
		endcase
		
	assign A0 = A[0];
	assign ShiftAdd = clr_P ? 8'h0 : (~sel_sum ? P : sum);
	
	assign data = lsb_out ? A : 8'hzz;
	assign data = msb_out ? P : 8'hzz;
endmodule
```
#### 8.1.3.4 乘法器的控制器
```Verilog
`define idle   4'b0000
`define init   4'b0001  //两个初始状态
`define m1     4'b0010
`define m2     4'b0011  
`define m3     4'b0100
`define m4     4'b0101  
`define m5     4'b0110
`define m6     4'b0111  
`define m7     4'b1000
`define m8     4'b1001  //八个相乘状态
`define rslt1  4'b1010
`define rslt2  4'b1011  //两个结束状态

module controller (input clk, start, A0,  output reg clr_P, load_P, load_B, msb_out,  lsb_out, sel_sum, output reg load_A, Shift_A, done);  
	reg [3:0] current;
	always @(posedge clk) begin  
		clr_P = 0; load_P = 0; load_B = 0; msb_out = 0;  
		lsb_out = 0; sel_sum = 0; load_A = 0; Shift_A = 0; done = 0;
		case (current)  
			`idle: 
				if (~start) begin
					current <= `idle;  
					done = 1;  
				end else begin  
					current <= `init;
					load_A = 1; 
					clr_P = 1;
					load_P = 1; 
				end
			`init: begin  
				current <= `m1; 
				load_B = 1;
			end
			`m1,`m2,`m3,`m4,`m5,`m6,`m7,`m8: begin  
				current <= current + 1;  
				Shift_A = 1;
				load_P = 1;
				if (A0) sel_sum = 1;  
			end  
			`rslt1: begin 
				current <= `rslt2;  
				lsb_out = 1;  
			end  
			`rslt2: begin 
				current <= `idle;  
				msb_out = 1;  
			end  
		default: current <= `idle;  
		endcase  
	end  
endmodule
```
#### 8.1.3.5 乘法器的顶层代码
```Verilog
module Multiplier (input clk, start, inout [7:0] databus, output lsb_out, msb_out, done);  
	wire clr_P, load_P, load_B, lsb_out, msb_out, sel_sum, load_A, Shift_A;
	  
	datapath dpu(clk, clr_P, load_P, load_B, msb_out, lsb_out, sel_sum, load_A, Shift_A, databus, A0);  
	controller cu(clk, start, A0, clr_P, load_P, load_B, msb_out, lsb_out, sel_sum, load_A, Shift_A, done);  
endmodule
```
### 8.1.4 乘法器的测试
- 对时序乘法器进行自动检查的交互式测试平台
```Verilog
module test_multiplier;  
	reg clk, start, error;  
	wire [7:0] databus;  
	wire lsb_out, msb_out, done;  
	reg [7:0] mem1[0:2], mem2[0:2];  
	reg [7:0] im_data, opnd1, opnd2;  
	reg [15:0] expected_result, multiplier_result;  
	integer idx;
	Multiplier uut ( clk, start, databus, lsb_out, msb_out, done );

	initial begin: Apply_Data ... end // 8.1.4.1 
	initial begin: Apply_Start ... end // 8.1.4.2 
	initial begin: Expected_Result ... end // 8.1.4.3 
	always @(posedge clk)  
		begin: Actual_Result ... end // 8.1.4.4 
	always @(posedge clk)  
		begin: Compare_Results ... end // 8.1.4.5
	always #50 clk = ~clk;  
	assign databus=im_data;  
endmodule
```
#### 8.1.4.1 读取数据文件
```Verilog
initial begin: Apply_Data  
	idx = 0;  
	$readmemb ("data1.dat", mem1);  
	$readmemb ("data2.dat", mem2);  
	repeat (3) begin  
		#300 im_data = mem1 [idx];  //先送入，看做B
		#100 im_data = mem2 [idx];  //晚100ns送入，被看做A
		#100 im_data = 8'hzz;  //数据送入后，释放databus
		idx = idx+1;  
		#1000;  
	end   
	#200 $stop;  
end
```
#### 8.1.4.2 产生开始信号
```Verilog
initial begin: Apply_Start  
	clk = 1'b0; start = 1'b0; im_data = 8'hzz;  
	#200;  
	repeat (3) begin  //产生start信号的正脉冲
		#50 start = 1'b1;  
		#100 start = 1'b0;  
		#1350;  
	end
end
```
#### 8.1.4.3 计算希望得到的结果
```Verilog
initial begin: Expected_Result  
	error = 1'b0;  
	repeat (3) begin  
		wait ( start==1'b1 );  
		@ ( databus );  
		opnd1 = databus;  
		@ ( databus );  
		opnd2 = databus;  
		expected_result = opnd1 * opnd2; //直接计算
	end  
end
```
#### 8.1.4.4 读取乘法器的输出
```Verilog
always @(posedge clk) begin: Actual_Result  
	if (msb_out) multiplier_result[15:8] = databus;  
	if (lsb_out) multiplier_result[7:0] = databus;  
end
```
#### 8.1.4.5 比较结果
```Verilog
always @(posedge clk) begin: Compare_Results  
	if (done)  
	if (multiplier_result != expected_result) error = 1;  
	else error = 0;  
end
```
## 8.2 冯诺依曼处理器模型
### 8.2.1 处理器与存储器模型














![Lecture 8 RTL设计与测试-1746272803569.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272803569.png)


![Lecture 8 RTL设计与测试-1746272808914.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272808914.png)

![Lecture 8 RTL设计与测试-1746272811910.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272811910.png)

![Lecture 8 RTL设计与测试-1746272816800.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272816800.png)



![Lecture 8 RTL设计与测试-1746272821091.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272821091.png)



```Verilog
module AC (input [7:0] data_in, input load, clk, output reg [7:0] data_out);  
	always @(posedge clk)  
		if (load) data_out <= data_in;  
endmodule

module PC (input [5:0] data_in, input load, inc, clr, clk, output reg [5:0] data_out);  
	always @(posedge clk)  
		if (clr) data_out <= 6'b000_000;  
		else if (load) data_out <= data_in;  
		else if (inc) data_out <= data_out + 1;  
endmodule

module IR (input [7:0] data_in, input load, clk, output reg [7:0] data_out);  
	always @(posedge clk)  
		if (load) data_out <= data_in;  
endmodule

module ALU (input [7:0] a, b, input pass, add, output reg[7:0] alu_out);  
	always @(a or b or pass or add)  
		if (pass) alu_out = a;  
		else if (add) alu_out = a + b;  
		else alu_out = 0;  
endmodule
```




```Verilog
module DataPath (input ir_on_adr, pc_on_adr, dbus_on_data, data_on_dbus, ld_ir, ld_ac, ld_pc,inc_pc, clr_pc, pass, add, alu_on_dbus, clk, output [5:0] adr_bus, output [1:0] op_code, inout [7:0] data_bus);  
	wire [7:0] dubs, ir_out, a_side, alu_out;  
	wire [5:0] pc_out;

	IR ir (dubs, ld_ir, clk, ir_out);  
	PC pc (ir_out[5:0], ld_pc, inc_pc, clr_pc, clk, pc_out);  
	AC ac (dubs, ld_ac, clk, a_side);  
	ALU alu (a_side, {2'b00,ir_out[5:0]}, pass, add, alu_out);

	assign adr_bus = ir_on_adr ? ir_out[5:0] : 6'bzzzzzz;  
	assign adr_bus = pc_on_adr ? pc_out : 6'bzzzzzz;  
	assign dubs = alu_on_dbus ? alu_out : 8'bzzzzzzzz;  
	assign data_bus = dubs_on_data ? dubs : 8'bzzzzzzzz;  
	assign dubs = data_on_dbus ? data_bus : 8'bzzzzzzzz;  
	assign op_code = ir_out[7:6];  
endmodule

```

```Verilog
`define Reset    2'b00
`define Fetch    2'b01  
`define Decode   2'b10
`define Execute  2'b11

module Controller (input reset, clk, input [1:0] op_code, output reg rd_mem, wr_mem, ir_on_adr, pc_on_adr, dbus_on_data, data_on_dbus, ld_ir, ld_ac, ld_pc, inc_pc, clr_pc, pass, add, alu_on_dbus);  
	reg [1:0] present_state, next_state;
	
	always @(posedge clk)  
		if (reset) present_state <= `Reset;  
		else present_state <= next_state;
		
	always @(present_state or reset) begin : Combinational 
	rd_mem=1'b0; wr_mem=1'b0; ir_on_adr=1'b0; pc_on_adr=1'b0;  
	dbus_on_data=1'b0; data_on_dbus=1'b0; ld_ir=1'b0;  
	ld_ac=1'b0; ld_pc=1'b0; inc_pc=1'b0; clr_pc=1'b0;  
	pass=0; add=0; alu_on_dbus=1'b0;
		case (present_state)  
			`Reset: begin 
				next_state = reset ? `Reset : `Fetch;
				clr_pc = 1;
			end // End` Reset  
			`Fetch: begin next_state = `Decode;  
				pc_on_adr = 1; rd_mem = 1; data_on_dbus = 1;  
				ld_ir = 1; inc_pc = 1;  
			end // End `Fetch
			`Decode: begin  
				next_state = `Execute; // End` Decode  
			end  
			`Execute: begin 
				next_state =` Fetch;  
				case (op_code)  
					2'b00: begin  
						ir_on_adr = 1; rd_mem = 1;  
						data_on_dbus = 1; ld_ac = 1;  
					end  
					2'b01: begin  
						pass = 1;  
						ir_on_adr = 1; alu_on_dbus = 1;  
						dbus_on_data = 1; wr_mem = 1;  
					2'b10: ld_pc = 1;  
					2'b11: begin  
						add = 1; alu_on_dbus = 1; ld_ac = 1;  
					end  
				endcase  
			end // End `Execute 
			default : next_state =` Reset;  
		endcase  
	end  
endmodule
```

```Verilog
module AddingCPU (input reset, clk, output [5:0] adr_bus, output rd_mem, wr_mem, inout [7:0] data_bus);  
	wire ir_on_adr, pc_on_adr, dbus_on_data, data_on_dbus,  
	ld_ir, ld_ac, ld_pc, inc_pc, clr_pc, pass, add, alu_on_dbus;  
	wire [1:0] op_code;
	
	Controller cu (reset, clk, op_code, rd_mem, wr_mem, ir_on_adr, pc_on_adr, dbus_on_data, data_on_dbus, ld_ir, ld_ac, ld_pc, inc_pc, clr_pc, pass, add, alu_on_dbus);  
	DataPath dp (ir_on_adr, pc_on_adr, dbus_on_data, data_on_dbus,  
ld_ir, ld_ac, ld_pc, inc_pc, clr_pc, pass, add, alu_on_dbus, clk, adr_bus, op_code, data_bus);  
endmodule
```



```Verilog
module Test_AddingCPU;  
	reg reset = 1, clk = 0;  
	wire [5:0] adr_bus;  
	wire rd_mem, wr_mem;  
	wire [7:0] data_bus;  
	reg [7:0] mem_data = 8'b0;  
	reg control = 0;  
	integer HexFile, check;  
	AddingCPU UUT (reset, clk, adr_bus, rd_mem, wr_mem, data_bus);

	always #10 clk = ~clk;

	initial begin  
	Convert;  
	HexFile = $fopen("HexadecimalFile.mem", "r+"); #25 reset = 1'b0; #405 $fclose(HexFile);  
	$stop;  
	end

	always @(posedge clk) begin : Memory_Read_Write  
	// ...  
	end

	// ...

	task Convert;  
	// ...  
	endtask  
endmodule
```

```Verilog
always @(posedge clk) begin : Memory_Read_Write  
	control = 0;  
	#1;  
	if (rd_mem) begin  
		#1;  
		check = $fseek(HexFile, 4 * adr_bus, 0);  
		check = $fscanf(HexFile, "%h", mem_data);  
		control = 1;  
	end  
	if (wr_mem) begin  
		#1; 
		check = $fseek(HexFile, 4 * adr_bus, 0);  
		$fwrite(HexFile, "%h", data_bus);  
		$fflush(HexFile);  
	end  
end  
assign data_bus = (control) ? mem_data : 8'hZZ;
```

```Verilog
task Convert;  
	begin: block  
		reg [5:0] addr;  
		reg [3 * 8:1] opCode;  
		reg [7:0] data, writeData;  
		reg JustData;  
		integer i, HexFile, InstFile, check;  
		HexFile = $fopen("HexadecimalFile.mem");  
		for (i = 0; i < 64; i = i + 1) $fwrite(HexFile, "00\n");  
			$fflush(HexFile); $fclose(HexFile);  
			
		InstFile = $fopen("InstructionFile.mem", "r");  
		HexFile = \(fopen("HexadecimalFile.mem", "r+"); 
		while (\)fscanf (InstFile, "%h", addr) != -1) begin  
			check = $fseek(HexFile, addr * 4, 0);  
			check = $fgets(opCode, InstFile);  
			JustData = 0;  
			case (opCode)  
				"lda": writeData[7:6] = 0;  
				"sta": writeData[7:6] = 1;  
				"jmp": writeData[7:6] = 2;  
				"add": writeData[7:6] = 3;  
				":::": begin  
					JustData = 1;  
					check = $fscanf (InstFile, "%h", writeData);  
				end  
				default: begin  
					JustData = 1;  
					check = $fscanf (InstFile, "%h", writeData);  
				end  
			endcase  
			
			if(JustData == 0) begin  
				check = $fscanf (InstFile, "%h", data);  
				writeData[5:0] = data[5:0];  
			end  
			$fwrite(HexFile, "%h", writeData);  
			end  
			$fflush (HexFile); $fclose (HexFile); 
			$fclose(InstFile);  
		end  
	endtask
```


## 8.3 CPU 的设计与测试

![Lecture 8 RTL设计与测试-1746272835039.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272835039.png)


![Lecture 8 RTL设计与测试-1746272840208.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272840208.png)


![Lecture 8 RTL设计与测试-1746272843958.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272843958.png)

![Lecture 8 RTL设计与测试-1746272848641.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272848641.png)




![Lecture 8 RTL设计与测试-1746272853816.png](/img/user/attachment_manager/Lecture%208%20RTL%E8%AE%BE%E8%AE%A1%E4%B8%8E%E6%B5%8B%E8%AF%95-1746272853816.png)




```Verilog
module Sayeh ( clk, ReadMem, WriteMem, ReadIO, WriteIO, Databus, Addressbus, ExternalReset, MemDataready);  
	input clk;  
	output ReadMem, WriteMem, ReadIO, WriteIO;  
	inout [15:0] Databus;  
	output [15:0] Addressbus;  
	input ExternalReset, MemDataready;  
	wire [15:0] Instruction;  
	wire esetPC, PCplusI, PCplusl, RplusI, Rplus0, ...;  
	DataPath dp (clk, Databus, Addressbus, ResetPC, PCplusI, PCplusl, RplusI, Rplus0, ...);  
	controller ctr (ExternalReset, clk,ResetPC, PCplusI, PCplusl, RplusI, Rplus0, ...);  
endmodule
```

```Verilog
module SayehTestbench();  
	parameter totalAddrLen = 16;  
	parameter pageLen = 4;  
	parameter pageNumberLen = 2;  
	reg clk, ExternalReset, MemDataready;  
	reg [15:0] MemoryData;  
	wire [15:0] Databus;  
	wire [totalAddrLen - 1:0] Addressbus;  
	wire ReadMem, WriteMem, ReadIO, WriteIO;  
	wire [totalAddrLen - pageLen - 1:0] physicalAddr;  
	reg [15:0] SayehRAM [0:(1<<(totalAddrLen - pageLen)) - 1];  
	reg dirty;  
	reg [pageLen - 1:0] prePage;  
	reg [pageNumberLen * 8:1] pageNumber;  
	integer i, file;  
	always #20 clk = ~clk;  
	initial begin  
		Translate; //convert file  
		clk = 0;  
		ExternalReset = 0;  
		MemDataready = 0;  
		MemoryData = 16'bZ;  
		dirty = 0;  
		prePage = 15;  
		#05 ExternalReset = 1;  
		#81 ExternalReset = 0; //run CPU  
		#370000;  
		$stop;  
	end  
	always @(negedge clk) begin : MemoryRead  
		// ...  
	end  
	always @(negedge clk) begin : MemoryWrite  
		// ...  
	end  
endmodule
```










