---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 4 组合电路描述/"}
---


# 4. 组合电路描述
## 4.1 模块连线
- 默认情况下，模块端口是 wire 类型, 可取0,1, z 和 x 四个逻辑值之一
- Input; inout: 只能被声明成为 net 类型
- Output：默认是 net 类型，但可以被声明为 reg
- 延时： `not #5 g0(sel_,sel)`
## 4.2 门级逻辑
### 4.2.1 门原语
![Lecture 4 组合电路描述-1744342649526.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1744342649526.png)
- 门最多有两个延迟参数（$t_{plh}\ \\t_{phl}$）
- 若没有指明，则假定延迟值为 0
- 如果仅使用一个延迟参数，如 #3 ，则该参数值应用于所有门的输出转换
```verilog
nand #(3,5) gate1 (w,i1,i2,i3,i4)
```
### 4.2.2 用户定义原语
- User defined primitives (UDP)
	- 以关键词 `primitive` 开始，之后定义它的名称和端口，第一个端口为输出端
	- 可构成多达 10 个 输入和 1 个输出的组合功能
	- 使用真值表创建 UDP，要求包含所以情况
	- 可以用 X，？，但不能包括 Z
```Verilog
primitive maj3 (y, a, b, c); 
	output y; 
	input a, b, c; 
	table 
		0 0 ? : 0;
		0 ? 0 : 0;
		? 0 0 : 0;
		1 1 ? : 1;
		1 ? 1 : 1;
		? 1 1 : 1;
	endtable 
endprimitive
```
### 4.2.3 延迟格式
- 延迟表达式： `min：typ：max` 

### 4.2.4 模块参数
- 模块参数（module parameter）
	- 局部参数（`localparam`）
	- 全局参数（`parameter` ）
- 指定参数（`specify parameter`）

- 命名参数赋值 `maj3_p #(.tplh2(7),.tplh1(6)) MUT4(aa,bb,cc,y4);`
- 参数重载 `defparam MUT.tplh2 = 7;`
## 4.3 层次化结构

### 4.3.1 简单层次
- 顺序连接（ordered connection）
```Verilog
module add_1bit_p (input a,b,ci, output sum co)
	xor_p xr1 (a,b,ci,sum);
	maj3_p mj1 (a,b,ci,co);
endmodule
```

- 命名连接（named connection）
```verilog
module add_1bit_p_named (input a,b,ci, output sum,co)
	
	xor_p xr1 (.a(a), .b(b), .c(ci), .y(sum));
	maj3_p mj1 (.a(a), .b(b), .c(ci), .y(co));
endmodule
```

### 4.3.2 向量声明
通过向量变量来构建更大的模块
```Verilog
module add_4bit (input [3:0] a,b, input ci,
				output [3:0] s, output co);
	wire c1,c2,c3;
	
	add_1bit_p fa0 (a[0],b[0],ci,s[0],c1);
	add_1bit_p fa1 (a[1],b[1],c1,s[1],c2);
	add_1bit_p fa2 (a[2],b[2],c2,s[2],c3);
	add_1bit_p fa3 (a[3],b[3],c3,s[3],c0);
	
endmodule

或者采用命名连接：
add_1bit_p fa1 (.a(a[1]),.b(b[1])),.ci(c1),.sum(s[1],.co(c2));
```
![Lecture 4 组合电路描述-1744381937751.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1744381937751.png)

### 4.3.3 迭代结构
#### 4.3.3.1 实例数组
在单个语句中例化相似模块的简易方法
```Verilog
module add_4bit_vec (input [3:0] a,b, input ci,
					output [3:0] s, output co);
	wire c1, c2, c3;
	add_1bit_p fa[3:0] (a,b,{c3,c2,c1,ci}, s,{co,c3,c2,c1});
endmodule

并置运算{c3,c2,c1,ci}形成一个向量
```

#### 4.3.3.2 生成多个实例
生成 (generate) 语句
```verilog
module add_4bit_gen (a,b,ci,s,co);
	parameter SIZE = 4;
	input [SIZE-1:0] a, b;
	input ci;
	output [SIZE-1:0] s;
	wire [SIZE:0] carry;
	genvar i;
	
	assign carry[0] = ci;
	assign co = carry[SIZE];
	
	generate
		for (i=0; i<SIZE; i=i+1) begin : full_adders
			add_1bit_p fa (a[i], b[i], carry[i], s[i], carry[i+1]);
		end
	endgenerate
endmodule

可以通过full_adders[3].fa.sum引用add_1bit_p的输出sum

也可以采用generate-if语句将add_1bit_p的第0个和第SIZE-1个实例与该模块其余的实例分隔开来

module add_4bt_genif (a,b,ci,s,co);
	parameter SIZE = 4;
	input [SIZE-1:0] a, b;
	input ci;
	output [SIZE-1:0] s;
	output co;
	wire [SIZE-1:0] carry;
	genvar i;
	
	assign carry[0] = ci;
	assign co = carry[SIZE];

	generate
		for(i=0; i<SIZE; i=i+1) begin : full_adders
			if(i==0) add_1bit_p fa (a[0], b[0], ci, s[0], carry[0]);
				else if(i==SIZE-1)
					add_1bit_p fa(a[i],b[i],carry[i-1],s[i],co);
			else add_1bit_p fa(a[i],b[i],carry[i-1],s[i],carry[i]);
		end
	endgenerate
endmodule		
```

### 4.3.4 模块路径延迟
- 分布式延迟：从下层元件计算出延迟总时序
- 模块路径延迟：引脚到引脚延迟，是在一个模块内的指定模块（specify block）定义的，指定输入到输出路径的延迟
- 模块路径延时可以与门级分布延迟同时存在，但只有当他比所以内部分布延迟之和大时，才会影响输出时序
- 
```Verilog
module add_1bit_p2p (input a,b,ci output sum co);
	specify
		(a,b,ci *> co) =12 ;
		(a,b,ci *> sum) = 15;
	endspecify
	
	xor_p xr1 (.a(a), .b(b), .c(ci), .y(sum));
	maj3_p mj1 (.a(a), .b(b), .c(ci), .y(co));
endmodule
```
![Lecture 4 组合电路描述-1744383931696.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1744383931696.png)

## 4.4 赋值语句中的描述表达式
### 4.4.1 按位运算符
直接用按位运算符进行例化
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 3 Verilog语言的概念#3.2.7.3 布尔运算符\|Lecture 3 Verilog语言的概念#3.2.7.3 布尔运算符]]
```verilog
module add_1bit (input a,b,ci, output s,co);
	assign #(10) s = a^b^ci;
	assign #(8) co = (a&b) | (b&ci) | (a&ci);
endmodule
```
### 4.4.2 并置运算符
```verilog
module add_1bit (input a,b,ci, output s,co);
	assign #(3,4) {co, s} = {(a&b) | (b&ci) | (a&ci), a^b^ci};
endmodule
```
### 4.4.3 向量运算
```verilog
module comp_4bit (ingput [3:0] a,b, output eq);
	wire [3:0] im;
	assign im = a ^ b;
	assign eq = ~| im; // im全为0时，eq=1
endmodule
```
### 4.4.4 条件运算
- `a ? b : c` `if then else`
- 当条件有一位是 `Z` 或者 `X` 时，会出现不确定条件
	- 如果两输入对应位相同 0 或 1，则该输出对应位与输入相同，若不同则为 X；
	- 如 `i1 = 0Z11，i2= 1Z1X`，则输出为 `XX1X`
- 条件运算可以嵌套
```Verilog
module dcd2_4 (inout a,b output d0, d1, d2, d3);
	assign
		{d3, d2, d1, d0} = ( {a, b} == 2'b00) ? 4'b0001 :
		{d3, d2, d1, d0} = ( {a, b} == 2'b01) ? 4'b0010 :
		{d3, d2, d1, d0} = ( {a, b} == 2'b10) ? 4'b0100 :
		{d3, d2, d1, d0} = ( {a, b} == 2'b11) ? 4'b1000 :
		4'b0000;
endmodule
```
### 4.4.5 赋值中的算数表达式
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 3 Verilog语言的概念#3.2.7.1 基本运算符\|Lecture 3 Verilog语言的概念#3.2.7.1 基本运算符]]
- 使用算数表达式 `+，-，*，/，**，`
- 注意赋值和运算中的大小匹配
```verilog
module add_4bit (input [3:0] a,b, input ci, output [3:0] s, output co);
	assign {co，s} = a + b + ci;
endmodule
```
### 4.4.6 表达式中的函数
- 类似编程语言，通过函数名返回函数值
- 函数内部不允许使用时序语句和非阻塞过程赋值
- 当调用一个函数时，变量的顺序必须与函数输入的声明一致
```Verilog
module add_1bit_f (input a, b, ci, output s, co);
	
	function [1:0] adder (input a,b,c)
	begin 
		adder = {(a & b) | (b & c) | (a & c), a^b^c};
	end
	endfunction
	
	assign #(3, 4) {co, s} = adder (a, b, ci);
endmodule
```
### 4.4.7 总线结构
- 总线可以被声明为 `wire，wand，wor, tri0和tri1`
```Verilog
wire [n-1 : 0] BUSA; //如果总线由多个源同时驱动，则总线的输出是所有有效源按位连线的结果
wand [n-1 : 0] BUSA; //总线的输出变成所有有效源按位线与

wor [n-1 : 0] BUSA; //总线每一位的功能是所有有效源按位线或的结果

wire [n-1 : 0] BUSA;
parameter [n-1 : 0] inactive = n'bZZ...Z;//wand:n'b111; wor:n'b000
assign BUSA = sel1 ? Source1 : inactive;
assign BUSA = sel2 ? Source2 : inactive;
...
assign BUSA = selm ? Sourcem : inactive;
```
![Lecture 4 组合电路描述-1744982017249.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1744982017249.png)
### 4.4.8 线网声明赋值
- 线网声明与 `assign` 语句组合，对应的延时为线延迟
```Verilog
module mux2_1 (input i0, i1, s, output y);
	wire #(0.6, 0.8) im0 = i0 & ~s, im1 = i1 & s;
	assign #（3, 4） y = im0 | im1;
endmodule
```
## 4.5 行为组合描述
- 过程语句提供了一种描述硬件的机制，称这种抽象级别为行为级
### 4.5.1 简单过程块
- `always，initial`
- `always` 隐含无限循环，除非被流程控制语句暂停
### 4.5.2 时序控制
``` Verilog
always @（*）//下文所有信号都包含
always @ （a,b,c） 等价于 always @ (a or b or c) 
a,b,c为敏感列表，等待a,b或c事件出现，就进入该过程块

一个过程块内的多个过程语句必须用关键字begin和end进行封装

延迟控制语句
module maj3 (input a,b,c output reg y);
	always @(a,b,c) #5 y =（a&b）|（b&c）|（a&c）;
endmodule
```
### 4.5.3 内部指定延迟
```Verilog
module maj3 (input a,b,c output reg y);
	always @(a,b,c) y = #5（a&b）|（b&c）|（a&c）;
endmodule
```
### 4.5.4 阻塞和非阻塞赋值
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 3 Verilog语言的概念#3.3.2 过程赋值语句\|Lecture 3 Verilog语言的概念#3.3.2 过程赋值语句]]
- 使用 `<=` 赋值符号
### 4.5.5 控制流程语句
-  `if-else，while，case，for`
```verilog
module priority_encoder (input i0,i1,i2,i3, output reg y1,y0,f);
	wire [3:0] im = {i3, i2, i1, i0};
	reg [2:0] indx;
	
	always @(im) begin
		{y1, y0} = 2'b00;
		f = 1'b0;
		for (indx = 0; indx < 4; indx = indx + 1)
		begin
			if (im[indx])
			begin
				{y1, y0} = indx;
				f = 1'b1;
			end
		end
	end
endmodule
```

`过程if-else语句`
![Lecture 4 组合电路描述-1745649271091.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649271091.png)

`过程case语句`
![Lecture 4 组合电路描述-1745649224251.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649224251.png)
`过程for循环`
![Lecture 4 组合电路描述-1745649187418.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649187418.png)

`过程while循环`
![Lecture 4 组合电路描述-1745649169428.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1745649169428.png)

### 4.5.6 多级描述
带有溢出，奇偶校验，比较输出和三态数据输出的 4-bit，4 功能 ALU
![Lecture 4 组合电路描述-1744982793945.png](/img/user/attachment_manager/Lecture%204%20%E7%BB%84%E5%90%88%E7%94%B5%E8%B7%AF%E6%8F%8F%E8%BF%B0-1744982793945.png)
```verilog
module alu_4bit (a,b,f,oe,y,p,ov,a_gt_b,a_eq_b,a_lt_b);
	input [3:0] a, b;
	input [1:0] f;
	input oe;
	output [3:0] y;
	output p, ov, a_gt_b, a_eq_b, a_lt_b;
	reg ov, a_gt_b, a_eq_b, a_lt_b;
	
	reg [4:0] im_y;
	
	always @ (a or b or f ) begin : arithmethic
		ov = 1'b0;
		im_y = 0;
		case ( f ) 
			2'b00: 
				begin
					im_y = a + b;
					if ( im_y > 5'b01111) ov = 1'b1;
				end
			2'b01: 
				begin
					im_y = a + b;
					if ( im_y > 5'b01111) ov = 1'b1;
				end
			2'b10: im_y [3:0] = a & b;
			2'b11: im_y [3:0] = a ^ b;
			default im_y [3:0] = 4'b0000;
		endcase
	end
	
	always @ (a or b) begin : compare
		if (a > b) {a_gt_b, a_eq_b, a_lt_b} = 3'b100;
		else if (a < b) {a_gt_b, a_eq_b, a_lt_b} = 3'b001;
		else {a_gt_b, a_eq_b, a_lt_b} = 3'b010;
	end
	
	assign p = ^im_y [3:0];
	
	assign y = oe ? im_y[3:0] : 4'bz;

endmodule
```
## 4.6 组合综合

### 4.6.1 门级综合
- 门级设计是可综合的，但是大多数综合工具不允许使用用户定义原语
- 在预综合描述中也不许使用开关级原语
- 可以使用三态门

### 4.6.2 连续赋值综合
- 通常，综合工具完成逻辑优化，这样可使设计者将精力更多地集中在功能设计上，而不是陷入门级或结构级优化的细节设计中

### 4.6.3 行为综合
- 输入敏感性：`always` 块的敏感列表中要包含它的所有输入，可用 `*` 暗指所有输入
- 输出赋值：输出不能出现锁存现象，即所有输出都有对应的输出值

### 4.6.4 混合综合
- 门级、模块例化、`assign` 语句和 `always` 块可在一个可综合的描述中共存
