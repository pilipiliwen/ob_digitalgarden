---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 5 时序电路描述/"}
---


# 5. 时序电路描述
## 5.1 时序模型
在数字电路中，数据的存储由反馈或者由频繁刷新的门电容实现
- 反馈模型
![Lecture 5 时序电路描述-1745649414835.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649414835.png)
- 电容模型
![Lecture 5 时序电路描述-1745649427997.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649427997.png)
- 隐含模型
	- 门级或开关级编码：不利于仿真效率
	- 所以 Verilog 通常使用更抽象的模拟存储元件
	- 模型的门和晶体管细节不清楚，Verilog 提供时序检测结构

## 5.2 基本存储器元件
### 5.2.1 门级原语
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 4 组合电路描述#4.2.1 门原语\|Lecture 4 组合电路描述#4.2.1 门原语]]
利用门级原语构建存储器元件
```Verilog
全部由与非门构成的钟控SR锁存器
module latch_p #(parameter tplh=3, tphl=5) (input s,r,c, output q,q_b);
	wire _s,_r;
	nand #(tplh,tphl)
		g1 (_s,s,c),
		g2 (_r,r,c),
		g3 (q,_s,q_b),
		g4 (q_b,_r,q);
endmodule
```
![Lecture 5 时序电路描述-1745651297584.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745651297584.png)
```Verilog
主从D触发器
module master_slave (input d,c, output q,q_b);
	wire qm, qm_b;
	defparam master.tplh=4, master.tphl=4, slave.tplh=4, slave.tphl=4;
	latch_p
		master (d,~d,c,qm,qm_b),
		slave (qm,qm_v,~c,q,q_b);
endmodule
```
![Lecture 5 时序电路描述-1745651948521.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745651948521.png)
### 5.2.2 用户定义时序原语
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 4 组合电路描述#4.2.2 用户定义原语\|Lecture 4 组合电路描述#4.2.2 用户定义原语]]
- 一个书序 UDP 具有第四章所述组合 UDP 的格式
- 除了电路的输入和输出之外，还需要指定它的当前状态
- `?` 表示任意值， `-` 表示无变化
```Verilog
primitive latch(q,s,r,c);
	output q;
	reg q;
	input s,r,c;
	initial q = 1'b0;
	table
		//s r c   q   q+
		//------:---:---;
		  ? ? 0 : ? : - ;
		  0 0 1 : ? : - ;
		  0 1 1 : ? : 0 ;
		  1 0 1 : ? : 1 ;
	endtable
endmodule
```
### 5.2.3 使用赋值的存储器单元
```Verilog
module master_slave_p #(parameter delay=3) (input d,c, output q);
	wire 1m;
	assign #(delay) qm = c ? d : qm;
	assign #(delay) q = ~c ? qm : q;
endmodule
```
![Lecture 5 时序电路描述-1745652573068.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745652573068.png)

### 5.2.4 行为存储器单元
- 通常使用行为编码来对描述时序电路
- 通常使用非阻塞赋值来描述时序电路
```Verilog
D锁存器
module latch (inpput d, c, output reg q, q_b);
	alway @ (c or d)
		if (c) begin
			q <= #4 d;
			q_b <= #3 ~d;
endmodule


正沿触发的触发器
module d_ff (input d, clk, output reg q, q_b);
	always @ (posedge clk) begin
		q <= #4 d;
		q_b <= #3 ~d;
	end
endmodule



带同步控制的D触发器
module d_ff_sr_Synch (input d,s,r,clk, output reg q,q_b);
	always @(pesedge clk) begin
		if (s) begin
			q <= #4 1'b1;
			q_b <= #3 1'b0;
		end else if (r) begin
			q <= #4 1'b0;
			q_b <= #3 1'b1;
		end else begin
			q <= #4 d;
			q_b <= #3 ~d;
		end
	end
endmodule

带异步控制的D触发器
module d_ff_sr_Asynch (input d,s,r,clk, output reg q, q_b);
	always @ (posedge clk, posedge s, posedge r) begin
		if (s) begin
			q <= #4 1'b1;
			q_b <= #3 1'b0;
		end else if (r) begin
			q <= #4 1'b0;
			q_b <= #3 1'b1;
		end else begin
			q <= #4 d;
			q_b <= #3 ~d;
		end
	end
endmodule

虽然posedge用于clk, s, r,但异步控制触发器只对时钟边沿敏感，对于s和r则对电平敏感
```
![Lecture 5 时序电路描述-1745653887681.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745653887681.png)
```Verilog
使用wait的锁存器
进入always语句后每7ns重复一次
module latch (input d,c output reg q,q_b);
	always begin
		wait (c)
			#4 q <= d;
			#3 q_b <= ~d;
	end
endmodule


使用fork-join的触发器，无延迟累加，所有顺序语句并行执行
module d_ff (input d,clk, output reg q, q_b);
	alway @ (posedge clk)
		fork 
			#4 q <= d;
			#3 q_b <= ~d;
		join
endmodule


使用顺序assign和deassign的D触发器（少见）
module d_ff (input d,clk,s,r, output reg q,q_b);
	always @ (s) begin : force_a_1
		if (s)
			begin
				#6 assign q = 1'b1;
				#4 assign q_b = 1'b0;
			end else begin
				deassign q;
				deassign q_b;
			end
		end
	end
	
	always @ (r) begin : force_a_0
		if (r)
			begin
				#6 assign q = 1'b0;
				#4 assign q_b = 1'b1;
			end else begin
				deassign q;
				deassign q_b;
			end
		end
	end
	
	always @ (posedge clk) : clocked
		#4 q = d;
		#3 q_b = ~d;
	end
endmodule
```
### 5.2.5 触发器时序
建立时间：`$setup (d, posedge clk, 5);`
保持时间：`$hold (posedge clk, d, 3);`
建立时间和保持时间检测组合：`$setuphold (posedge clk, d, 5, 3);`

宽度：`$width (posedge r, 4);` 检测参考信号的特定沿到它的相反沿之间的时间
周期：`$period (negedge clk, 43);`检测参考信号的特点沿到它的相同沿之间的时间
```Verilog
module d_ff (input d,clk,s,r, output reg q, output q_b);
	specify 
		$setuphold (posedge clk, d, 5, 3);
		$width (posedge r, 4);
		$width (posedge s, 4);
		$period (negedge clk, 43);
	endspecify
	
	alway @(posedge clk or posedge s or posedge r)
		if (s) q <= #4 1'b1;
		else if (r) q <= #4 1'b0;
		else q <= #4 d；
	assign #2 q_b = ~q;
endmodule
```
![Lecture 5 时序电路描述-1745665837359.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745665837359.png)

### 5.2.6 存储器向量和数组
使用向量和数组对输入输出端口进行相应的声明即可。
- 带有三态输出的8-bit寄存器
```verilog
module vector _ff (input [7:0] d, input clk,rst,oe, output [7:0] q);
	reg [7:0] internal_q;
	always @(posedge clk)
		if (rst)
			#4 internal_q <= 8'b0000_0000;
		else
			#4 internal_q <= d;
	assign q = oe ? internal_q : 8'bZ;
endmodule
```
- 双向存储器
```Verilog
module Memory_2Power_M_by_N#(parameter M=3, N=4) (input [M-1:0] adr, input rd,wr, inout [N-1:0] data);
	reg [N-1:0] mem [0:2**M-1];
	reg [N-1:0] temp;
	assign data = rd ? temp : 'bZ;
	always @ (data, adr, rd, wr);
		begin
			if (wr)
				#4 mem [adr] = data;
			else if (rd)
				#4 temp = mem[adr];
			else
				#4 temp = 'bZ;
		end
	end
	initial $redamemh("mem.dat", mem);//读取外部数据文件，初始化存储器块
endmodule
```
## 5.3 功能寄存器
### 5.3.1 移位寄存器
- 基本移位寄存器
```Verilog
module shift_reg (input [3:0] d, input clk,ld,rst,l_r,s_in, output reg [3:0] q);
	always @(posedge clk) begin
		if (rst)
			#5 q <= 4'b0000;
		else if (ld)
			#5 q <= d;
		else if (l_r)
			#5 q <= {q[2:0],s_in};
		else
			#5 q <={s_in, q[3:1]};
	end
endmodule
```
![Lecture 5 时序电路描述-1745667550908.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745667550908.png)
- 通用移位寄存器
```Verilog
module shift_reg (input clk,rst,r_in,l_in,en,s1,s0, inout [7:0] io);
	reg [7:0] q_int;
	assign io = (en) ? q_int : 8'bZ;//利用中间reg类型数据给inout wire类型赋值
	always @ (posedge clk) begin
		if (rst)
			#5 q_int = 8'b0;
		else
			case ( {s1,s0})
				2'b01 : //Shift right
				q_int <= { r_in, q_int [7:1]};
				2'b10 : //Shift left
				q_int <= { q_int [6:0], l_in};
				2'b11 : // Parallel load
				q_int = io;
				default : //Do nothing
				q_int <= q_int;
			endcase
		end
	end
endmodule
```
- 具有独立寄存器块的多位移位寄存器
```Verilog
module shift_reg (input [3:0] d_in, input clk,sr,sl,ld,rst, input [1:0] s_cnt,output reg [3:0] q);
	reg [3:0] int_q;
	always @ (d_in,q,s_cnt,sr,sl,ld) begin: combinational
		if (ld)
			int_q = d_in;
		else if (sr)
			int_q = q >> s_cnt;//右移s_cnt位
		else if (sl)
			int_q = q << s_cnt;//左移s_cnt位
		else int_q = q;
	end
	always @ (posedge clk) begin: register
		if (rst) q <= 0;
		else q <= int_q;
	end
endmodule
```
![Lecture 5 时序电路描述-1745667515849.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745667515849.png)
### 5.3.2 计数器
- 加减计数器
```Verilog
module counter (input [3:0] d_in, input clk,rst,ld,u_d, output reg [3:0] q);
	always @(posedge clk) begin
		if (rst)
			q = 4'b0000;
		else if (ld)
			q = d_in;
		else if (u_d)
			q = q + 1;//加计数到1111时，再加1返回0000
		else
			q = q - 1;
	end
endmodule
```
- 格雷码计数器（通过构造转化功能的查表实现）
```Verilog
module gray_counter (input [3:0] d_in, input clk,rst,ld, output reg [3:0] q);
	reg [3:0] mem [0:15];
	reg [3:0] im_q;
	initial
		$readmemb("mem.dat",mem);
	always @ (d_in or ld or q) begin: combinational
		if (ld)
			im_q = d_in;
		else
			im_q = mem[q];
	end
	always @ (posedge clk) begin: register
		if (rst)
			q <= 4'b0000;
		else
			q <= im_q;
	end
endmodule
```
### 5.3.3 LFSR 和 MISR
- LFSR （线性反馈移位寄存器）用于伪随机数的生成
- 由 D 触发器和异或门构成的 LFSR。异或门的位置决定该电路的多项式，在此是 poly = 10101
- `seed` 是寄存器的初始，影响单个触发器的置位和复位输入
```Verilog
结构化代码
module dff (input clk,set,rsr,d, output reg q);
	always @ (posedge clk or posedge set or posedge rst)
		if (set)
			q <= 1'b1;
		else if (rst)
			q <= 1'b0;
		else
			q <= d;
endmodule

module structural_1fsr #(parameter [3:0] seed=4'b0) 
	(input clk,init,sin, output sout);
	wire im1,im2,im3,im4,im5;
	dff ff[3:0](clk,{4{init}}&seed,{4{init}&~seed,{im1,im2,im4,im5},{im2,im3,im5,sout}});
	xor (im1,sin,sout);
	xor (im4,im3,sout);

行为级代码
module behavioral)lfsr #(parameter [3:0] poly=0,seed=0)
	(input clk, init,sin ,output reg sout);
	reg [3:0] im_data;
	always @ (posedge clk or posedge init)
		begin
			if(init)
				im_data = seed;
			else
				im_data = {{sin^ im_data[0],imdata[3:1]^(poly[2:0]&{3{im_data[0]}}}));
		end
endmodule
```
![Lecture 5 时序电路描述-1746201544654.png|多项式为10101的LFSR](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746201544654.png)
- MISR（多输入特征寄存器）用于特征生成和数据压缩
```Verilog
module #(parameter [3:0] poly = 0) misr (input clk,rst,input [3:0] d_in, output reg [3:0] d_out);
	always @ (posedge clk)
		if (rst)
			d_out = 4'b0000;
		else
			d_out = d_in^({4{d_out[0]}}&poly)^{1'b0,d_out[3:1]};
endmodule
```
![Lecture 5 时序电路描述-1746249786763.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746249786763.png)

### 5.3.4 堆栈和队列
- FIFO (先进先出) 队列 
![Lecture 5 时序电路描述-1746256459218.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746256459218.png)
```Verilog
module fifo (input [7:0] data_in, input clk,rs,rd,wr,output empty,full, output reg [3:0] fifo_cnt, output reg [7:0] data_out);
	reg [7:0] fif_ram[0:7];
	reg [2:0] rd_ptr, wr_ptr;
	assign empty = (fifo_cnt == 0);
	assign full = (fifo_cnt == 8);
	always @ (posedge clk) begin: write
		if (wr && !full)
			fifo_ram[wr_ptr] <= data_in;
		else if (wr & wr)
			fifo_ram[wr_ptr] <= data_in;
	end
	always @ (posedge clk) begin: read
		if (rd && !empty)
			data_out <= fifo_ram[rd_ptr];
		else if (rd && wr && empty)
			data_out <= fifo_ram[re_ptr];
	end
	always @ (posedge clk) begin: pointer
		if (rst) begin
			wr_ptr <= 0;
			rd_ptr <= 0;
		end else begin
			wr_ptr <= ((wr&&!full)||(wr&&rd)?wr_ptr+1:wr_ptr);
			rd_ptr <= ((rd&&!empty)||(wr&&rd)?rd_ptr+1:rd_ptr);
		end
	end
	
	always @ (posedge clk) begin: count
		if (rst) fifo_cnt <= 0;
		else begin
			case ({wr,rd})
				2'b00 : fifo_cnt <= fifo_cnt;
				2'b01 : fifo_cnt <= (fifo_cnt==0) ? 0 : fifo_cnt-1;
				2'b10 : fifo_cnt <= (fifo_cnt==8) ? 8 : fifo_cnt+1;
				2'b11 : fifo_cnt <= fifo_cnt;
			endcase
		end
	end
endmodule	
```
## 5.4 状态机编码
### 5.4.1 Moore 状态机
- 一个 Moore 状态机是一种所有输出与电路时钟全同步的状态机
```Verilog
module moore_detector (input x,rst,clk,output z);
	localparam [1:0] reset=0,got1=1,got10=2,got101=3;
	reg [1:0] current;
	always @ (posedge clk) begin
		if (rst) current <= reset;
		else case (current)
			reset: begin
				if (x==1'b1) current <= got1;
				else current <= reset;
			end
			got1: begin
				if (x==1'b0) current <= got10;
				else current <= got1;
			end
			got10: begin
				if (x==1'b1) current <= got101;
				else current <= reset;
			got101: begin
				if (x==1'b1) current <= got1;
				else current <= got10;
			end
			default: beign
				current <= reset;
			end
		endcase
	end
	assign z = (current == got101) ? 1 : 0;
endmodule		
```
![Lecture 5 时序电路描述-1746257420954.png|A Moore 101 Detector](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746257420954.png)
```Verilog
`define  reset  3'b000
`define  got1   3'b001
`define  got10  3'b010
`define  got11  3'b011
`define  got101 3'b100
`define  got110 3'b101

module morre_detector (input x,rst,clk,output z);
	reg [2:0] current;
	always @ (posedge clkor posedge rst) begin
		if (rst) current <= `reset;
		else
			case (current)
				`reset:
					if (x==1'b1) current <= `got1;
					else current <= `reset;
				`got1:
					if (x==1'b0) current <= `got10;
					else current <= `got11;
				`got10:
					if (x==1'b1) current <= `got101;
					else current <= `reset;
				`got11:
					if (x=1'b1) current <= `got11;
					else current <= `got110;
				`got101:
					if (x==1'b1) current <= `got11;
					else current <= `got10;
				`got110:
					if (x==1'b1) current <= `got101;
					else current <= `reset;
				default:
					current <= `got101;
			endcase
	end
endmodule			
```
![Lecture 5 时序电路描述-1746257889337.png|Moore Machine Detecting 110/101](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746257889337.png)
### 5.4.2 Mealy 状态机
- Mealy 状态机与 Moore 状态机的不同之处是，它的输出有它的当前状态和输入决定
```Verilog
module mealy_detector (input x,rst,clk, output z);
	localparam [1:0]
		reset = 0, got1 = 1, got10 = 2;
	reg [1:0] current;
	always @ (posedge clk) begin
		if (rst) current <= reset;
		else case (current)
			reset:
				if (x==1'b1) current <= got1;
				else current <= reset;
			got1:
				if (x==1'b0) current <= got10;
				else current <= got1;
			got10:
				if (x==1'b1) current <= got1;
				else current <= reset;
			default:
				current <= reset;
		endcase
	end
	assign z = (current==got10 && x==1'b1) ? 1'b1 : 1'b0;
endmodule
```
![Lecture 5 时序电路描述-1746258633038.png|A 101 Mealy Machine](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746258633038.png)
### 5.4.3 Huffman 编码风格
- 一个数字系统的 `Huffman` 模型表征为一个带寄存器阵列反馈的组合快
- 用一条 `always` 语句描述寄存器部分，另一条并行语句描述组合部分
```Verilog
module moore_detector4 (input x, rst, clk, output reg z);  
	localparam [1:0]  
	reset=2'b00, got1=2'b01,  
	got10=2'b10, got101=2'b11;  
	reg [1:0] p_state, n_state;  
	always @(p_state or x) begin:combinational  
		n_state = reset;  
		z = 1'b0;  
		case (p_state)  
			reset:  
				begin  
					if( x==1'b1 ) n_state = got1;  
					else n_state = reset;  
					z = 1'b0;  
				end  
			got1:  
				begin  
					if( x==1'b0 ) n_state = got10;  
					else n_state = got1;  
				z = 1'b0;  
				end  
			got10:  
				begin  
					if( x==1'b1 ) n_state = got101;  
					else n_state = reset;  
					z = 1'b0;  
				end  
			got101:  
				begin  
					if( x==1'b1 ) n_state = got1;  
					else n_state = got10;  
					z = 1'b1;  
				end  
			default:  
				begin  
					n_state = reset;  
					z = 1'b0;  
				end  
			endcase  
		end  
	end
	
	always @ (posedge clk) begin: sequential
		if (rst) p_state <= reset;
		else p_state <= n_state;
	end
	
endmodule
```
![Lecture 5 时序电路描述-1746259187007.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746259187007.png)
### 5.4.4 多模块化描述风格
- 对于具有更多输入和输出及更复杂输出逻辑的设计，Combination 块可进一步划分为一个处理转换的块和另一个对电路的输出幅值的块
![Lecture 5 时序电路描述-1746259710500.png](/img/user/attachment_manager/Lecture%205%20%E6%97%B6%E5%BA%8F%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1746259710500.png)
### 5.4.5 基于 ROM 的控制器
- 为了替代采用 `case语句` 和 `if-else` 语句，可以用简单的存储器查找表来表述
## 5.5 时序综合
Depends on the library, the tools build circuits if necessary.
















Test:
```Verilog
module counter (input[3:0] d_in,input clk,rst,ld,u_d, output reg [3:0] q);
	reg [3:0] int_q;
	always @ (d_in, ld, u_d, q) begin: combinatianal
		if (ld)
			int_q = d_in;
		else if (u_d)
			int_q = q + 1；
		else
			int_q = q - 1;
	end
	always @ (posedge clk) begin: register
		if (rst) q <= 0;
		else q <= int_q;
	end
endmodule
```





