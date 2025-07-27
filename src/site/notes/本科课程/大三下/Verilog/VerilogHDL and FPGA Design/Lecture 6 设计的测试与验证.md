---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 6 设计的测试与验证/"}
---


# 6. 设计的测试与验证

## 6.1 测试平台
- Verilog testbench 例化待测模块，施加激励并观测其输出
### 6.1.1 组合电路测试
对简单算数逻辑单元进行测试：
```Verilog
module alu_4bit (a, b, f, oe, y, p, ov, a_gt_b, a_eq_b, a_lt_b);  
	input [3:0] a, b;  
	input [1:0] f;  
	input oe;  
	output [3:0] y;  
	output p, ov, a_gt_b, a_eq_b, a_lt_b;
	//...  
endmodule

module test_alu_4bit;  
	reg [3:0] a=4'b1011, b=4'b0110;  
	reg [1:0] f=2'b00;  
	reg oe=1;  
	wire [3:0] y;  
	wire p, ov, a_gt_b, a_eq_b, a_lt_b;
	alu_4bit cut( a, b, f, oe, y, p, ov, a_gt_b, a_eq_b, a_lt_b );
	initial begin  
		#20 b=4'b1011;  
		#20 b=4'b1110;  
		#20 b=4'b1110;  
		#80 oe=1'b0;  
		#20 $finish;  
	end
	always #23 f = f + 1;  
endmodule
```
![Lecture 6 设计的测试与验证-1746260628663.png](/img/user/attachment_manager/Lecture%206%20%E8%AE%BE%E8%AE%A1%E7%9A%84%E6%B5%8B%E8%AF%95%E4%B8%8E%E9%AA%8C%E8%AF%81-1746260628663.png)
### 6.1.2 时序电路测试
- 为了降低多个输入同时反转的概率，对时序电路的输入一般采用素数作为时间间隔
```Verilog
module #(parameter [3:0] poly=0) misr (input clk, rst,input [3:0] d_in, output reg [3:0] d_out );
	always @(posedge clk)  
		if ( rst )  
			d_out=4'b0000;  
		else  
			d_out=d_in^({4{d_out[0]}}&poly)^{1'b0,d_out[3:1]};  
endmodule

module test_misr;  
	reg clk=0, rst=0;  
	reg [3:0] d_in;  
	wire [3:0] d_out;
	misr #(4'b1100) MUT ( clk, rst, d_in, d_out );

	initial begin  
		#13 rst=1'b1;  
		#19 d_in=4'b1000;  
		#31 rst=0'b0;  
		#330 $finish;  
	end
	always #37 d_in = d_in + 3;  
	always #11 clk = ~clk;  
endmodule
```
![Lecture 6 设计的测试与验证-1746260893569.png](/img/user/attachment_manager/Lecture%206%20%E8%AE%BE%E8%AE%A1%E7%9A%84%E6%B5%8B%E8%AF%95%E4%B8%8E%E9%AA%8C%E8%AF%81-1746260893569.png)
## 6.2 测试平台技术
### 6.2.1 测试数据
- 通常在 `initial` 块中对 `reg` 类型变量初始化
```Verilog
module moore_detector (input x, rst, clk, output z);
	parameter [1:0] a=0, b=1, c=2, d=3;  
	reg [1:0] current;
	always @(posedge clk)  
		if ( rst )  
			current = a;  
		else  
			case ( current )  
				a : current = x ? b : a;  
				b : current = x ? b : c;  
				c : current = x ? d : a;  
				d : current = x ? b : c;  
		default : current = a;  
	endcase 
	assign z = (current==d) ? 1'b1 : 1'b0;  
endmodule

module test_moore_detector;  
	reg x, reset, clock;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );
	
	initial begin  
		clock=1'b0; x=1'b0; reset=1'b1;  
	end
	initial #24 reset=1'b0;  
	always #5 clock=~clock;  
	always #7 x=~x;  
endmodule
```
### 6.2.2 对仿真的控制
- 使用 `$stop,$finifh` 停止仿真
```Verilog
module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial #24 reset=1'b0;  
	always #5 clock=~clock;  
	always #7 x=~x;  
	initial #189 $stop;  
endmodule

module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial begin  
		#24 reset=1'b0;  
		#165 $finish;  
	end
	always #5 clock=~clock;  
	always #7 x=~x;  
endmodule
```
### 6.2.3 设置数据限制
- 采用 `$random` 对输入 `x` 生成随机数据
- 采用 `repeat` 限制时钟的总变化次数
```Verilog
module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial #24 reset=1'b0;  
	initial repeat(13) #5 clock=~clock;  
	initial repeat(10) #7 x=$random;  
endmodule
```
### 6.2.4 采用同步数据
```Verilog
module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial #24 reset=0;  
	initial repeat(13) #5 clock=~clock;  
	initial forever @(posedge clock) #3 x=$random;  
endmodule
```
### 6.2.5 输出结果的同步显示
- 应用任务函数 `$displayb` 输出 `z` 的结果
- 采用 `$monitor` 显示寄存器 `current` 的值，另外使用一个 `always` 块来显示输出结果
- `initial` 语句用来显示 `MUT.current` 的值，`%b` 输出二进制，`%t` 显示时间
```Verilog
module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial #24 reset=0;  
	initial repeat(13) #5 clock=~clock;  
	initial forever @(posedge clock) #3 x=$random;  
	initial forever @(posedge clock) #1 $displayb(z);  
endmodule


module test_moore_detector;  
	reg x=0, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, reset, clock, z );

	initial #24 reset=0;  
	initial repeat(19) #5 clock=~clock;  
	initial forever @(posedge clock) #3 x=$random;  
	initial $monitor("New state is %d and occurs at %t", MUT.current, $time);  
	always @(z) $display("Output changes at %t to %b", $time, z);  
endmodule
```
![Lecture 6 设计的测试与验证-1746262743691.png](/img/user/attachment_manager/Lecture%206%20%E8%AE%BE%E8%AE%A1%E7%9A%84%E6%B5%8B%E8%AF%95%E4%B8%8E%E9%AA%8C%E8%AF%81-1746262743691.png)
### 6.2.6 交互式测试平台
- 使用 `wait` 语句，等待测试模块结果变化
```Verilog
//一个有开始和复位控制的1101Moore序列检测器
module moore_detector (input x, start, rst, clk, output z);  
	parameter a=0, b=1, c=2, d=3, e=4;  
	reg [2:0] current;

	always @(posedge clk)  
	if ( rst )  
		current <= a;  
	else if ( ~start )  
		current <= a;  
	else  
		case ( current )  
			a : current <= x ? b : a;  
			b : current <= x ? c : a;  
			c : current <= x ? c : d;  
			d : current <= x ? e : a;  
			e : current <= x ? c : a;  
			default: current <= a;  
		endcase
	
	assign z = (current==e);  
endmodule

module test_moore_detector;  
	reg x=0, start, reset=1, clock=0;  
	wire z;
	moore_detector MUT ( x, start, reset, clock, z );

	initial begin  
		#24 reset=1'b0; start=1'b1;  
		wait (z==1'b1);  
		#11 start=1'b0;  
		#13 start=1'b1;  
		repeat(3) begin  
			#11 start=1'b0;  
			#13 start=1'b1;  
			wait (z==1'b1);  
		end  
		#50 $stop;  
	end
	
	always #5 clock=~clock;  
	always #7 x=$random;  
endmodule
```
![Lecture 6 设计的测试与验证-1746262950377.png](/img/user/attachment_manager/Lecture%206%20%E8%AE%BE%E8%AE%A1%E7%9A%84%E6%B5%8B%E8%AF%95%E4%B8%8E%E9%AA%8C%E8%AF%81-1746262950377.png)
- 显示任务的交互式测试平台
```Verilog
module test_moore_detector; 
	reg x=0, start, reset=1, clock=0; 
	wire z;
	moore_detector MUT ( x, start, reset, clock, z );
	
	initial begin 
		#24 reset=1'b0; 
		start=1'b1; 
	end
	always begin : Output_Display 
		wait (MUT.current == MUT.e);
		$display ("$display task shows: The output is %b ", z);
		$strobe ("$strobe task shows: The output is %b ", z);
		#2 $stop; 
	end
	
	always #5 clock =~ clock;
	always #7 x = $random; 
endmodule
```
### 6.2.7 随机时间间隔
```Verilog
module test_moore_detector;
	reg x, start, reset, clock;
	wire z; 
	reg [3:0] t;
	moore_detector MUT ( x, start, reset, clock, z );

	initial begin: running
		clock <= 1'b0; x <= 1'b0;
		reset <= 1'b1; reset <= #7 1'b0;
		start <= 1'b0; start <= #17 1'b1;
		repeat (13) begin 
			@( posedge clock ); 
			@( negedge clock ); 
		end 
		start=1'b0; 
		#5; 
		$finish; 
	end
	
	always #5 clock=~clock;
	always begin 
		t = $random;
		#(t) x = $random;
	end 
endmodule
```
### 6.2.8 数据缓存的应用
- 利用缓存器保存数据并将其送给待测模块的测试平台
- 将缓存里的数据逐位移进 `Moore_detector` 的输入信号 `x`
- 循环移位保证缓存的数据可以重复使用
```Verilog
module test_moore_detector;  
	reg x=0, rst, start, clk=0;  
	wire z;  
	reg [18:0] buffer;
	moore_detector MUT ( x, start, rst, clk, z );

	initial buffer = 19'b000110110111001001;
	initial begin  
		rst=1'b1; start=1'b0;  
		#29 rst=1'b0;  
		#29 start=1'b1;  
		#500 $stop;  
	end
	always @(posedge clk) 
		#1 {x, buffer} = {buffer, x};  
	always #5 clk = ~clk;  
endmodule
```
## 6.3 设计的验证
验证的自动化设计
- 形式验证
	- 不需要测试平台，也没有数据生成与分析的问题
	- 设计者通过写出特征来检查自己的设计
	- 但对于设计而言，只有形式验证是不够的
- 断言验证
	- 通过对设计添加监控器来改善它的可观察性
	- 如果仿真期间在某一点出现断言失败，监控器会发出警告
## 6.4 断言验证
- 设计完成后，通常把断言放在设计的关键点上，检查关键功能点
### 6.4.1 开放式验证库（OVL）
- 设计者能够定义自己的断言集合，并把他们用在设计中
![Lecture 6 设计的测试与验证-1746270598648.png](/img/user/attachment_manager/Lecture%206%20%E8%AE%BE%E8%AE%A1%E7%9A%84%E6%B5%8B%E8%AF%95%E4%B8%8E%E9%AA%8C%E8%AF%81-1746270598648.png)
- 断言与例化模块一样放在代码里
	- 典型的静态参数包括：失败程度、向量长度、时钟数、时间帧描述、显示的失败信息
	- 动态参数：参考时钟、开始信号、复位信号和检查表达式
```Verilog
assert_name #(static_parameters) instance_name (dynamic_arguments);
```

### 6.4.2 断言监视器的应用
-  `assert_always`
	- 在每个指定的时钟沿 `clk` 检查表达式 `test_expr` 是否满足，当表达式不满足时，断言告警并显示相应的信息 `msg`
	- `severity_level = 1` 表示断言告警时继续进行仿真
```Verilog
assert_always #(severity_level, property_type, msg, coverage_level) instance_name (clk, reset_n, test_expr)

 BCD_Counter (input rst, clk, output reg [3:0] cnt);  
	always @(posedge clk) begin  
		if ( rst || cnt >= 10 )  
			cnt = 0;  
		else  
			cnt = cnt + 1;  
		end  
	assert_always #(1, 0, "Err: Non BCD Count", 0)  
	AA1 (clk, 1'b1, (cnt >= 0) && (cnt <= 9));  
endmodule
```

- `assert_change`
	- 确认在开始事件发生后给定的时钟周期内，检查测试表达式的值的变化

- `assert_one_hot`
	- 检查 n 比特检查表达式中是否只有一个比特的的值为 1

- `assert_cycle_sequence`
	- 检查给定时钟周期内是否发生了一系列事件
	- 主要用于检测状态机

- `assert_next`
	- 检查开始事件与结束事件之间经过了指定的时钟周期

## 6.5 基于文本的测试平台
通过读写外部文件的系统任务来进行测试
