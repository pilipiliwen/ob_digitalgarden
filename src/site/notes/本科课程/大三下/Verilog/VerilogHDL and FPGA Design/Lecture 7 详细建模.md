---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 7 详细建模/"}
---


# 7. 详细建模
- 本章对结构进行详细建模的 Verilog 特性是不可综合的，只用来生成精确的模型
- 通常用于代工厂或IC公司的库开发。
## 7.1 开关级建模
- 晶体管级建模：用输入输出为模拟信号的晶体管搭建硬件模型
- 门级建模：用输入输出为数字信号的逻辑门搭建硬件模型
- 开关级建模：
	- 介于两种方案之间，硬件结构用晶体管级描述，而信号为数字信号
	- 晶体管表现为通断形式的开关
### 7.1.1 开关级原语
- 单向晶体管将其输入传给输出，双向晶体管可以控制数据双向传输
- 阻抗型开关把输入传到输出时，逻辑值的强度会衰减
![Lecture 7 详细建模-1746272562172.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272562172.png)

`drain source gate`
`#(x,y,z) 输出为1，延迟x；输出为0，延迟y；输出为Z，延迟z`
![Lecture 7 详细建模-1746272572695.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272572695.png)

- 箭头方向是载流子方向
![Lecture 7 详细建模-1747969176078.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1747969176078.png)
### 7.1.2 基本开关
- 调用单向开关时，如果指定的延迟数目少于 3，那么未指定的延迟去指定值中的最小值
```Verilog
module an_nmos (input a,b, output y);
	nmos #(3,4,5) (y,a,b,);
endmodule
```
![Lecture 7 详细建模-1746272607666.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272607666.png)

![Lecture 7 详细建模-1746272610688.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272610688.png)

### 7.1.3 CMOS 门
- 开关级两输入与非门
```Verilog
module nand2_1d (input a,b, output y);
	supply0 Gnd;
	supply1 Vdd;
	wire im1;
	
	pmos #(4) g1 (y,Vdd,a);
	pmos #(4) g2 (y,Vdd,b);
	nmos #(3) g3 (y.im1,a);
	nmos #(3) g4 (im1,Gnd,b);
endmodule
```
![Lecture 7 详细建模-1746272614141.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272614141.png)

![Lecture 7 详细建模-1746272620826.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272620826.png)

- 4 输入 AOI (AND-OR-INVERT)
```Verilog
module aoi_3d (input a, b, c, d, output y);  
	supply0 Gnd;  
	supply1 Vdd;  
	wire im1, im2, im3;
	
	pmos #(3,5,7)  
		g1( im1, Vdd, a ),  
		g2( im1, Vdd, b ),  
		g3( y, im1, c ),  
		g4( y, im1, d );  
	nmos #(2,4,6)  
		g5( y, im2, a ),  
		g6( im2, Gnd, b ),  
		g7( y, im3, c ),  
		g8( im3, Gnd, d );  
endmodule
```
![Lecture 7 详细建模-1746272624568.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272624568.png)
### 7.1.4 传输门逻辑
- 可以通过 NMOS 和 PMOS 管的电特性来搭建简单的开关级电路，以实现特定的布尔代数功能
- 二选一多路选择器
```Verilog
module mux (input io, ii, so, sl, output y);  
	wire y;  
	nmos #(4)  
		g1( y, io, so ),  
		g2( y, ii, sl );  
endmodule
```
![Lecture 7 详细建模-1746272628459.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272628459.png)
- 4 比特移位寄存器
```Verilog
module shifter (input i3, i2, i1, i0, sin, ls, output y3, y2, y1, y0);  
	supply1 Vdd;  
	supply0 Gnd;

	nmos #(3)  
		(y0, sin, ls),  
		(y0, i0, ls_b),  
		(y1, i0, ls),  
		(y1, i1, ls_b),  
		(y2, i1, ls),  
		(y2, i2, ls_b),  
		(y3, i2, ls),  
		(y3, i3, ls_b);  
	nmos #(5) (ls_b, Gnd, ls);  
	pmos #(5) (ls_b, Vdd, ls);  
endmodule
```
![Lecture 7 详细建模-1746272631639.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272631639.png)

![Lecture 7 详细建模-1746272637223.png|541x244](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272637223.png)
- 桶型移位寄存器
```Verilog
module barrel_shifter (i, l, y);  
	parameter SIZE = 4;  
	input [SIZE-1:0] i, l;  
	output [SIZE-1:0] y;  
	genvar a, b;
	
	generate for (a=0; a<SIZE; a=a+1) begin:row  
		for (b=0; b<SIZE; b=b+1) begin:col  
			if (b>=a)  
				nmos #2 (y[a], i[b], l[b-a]);  
			else  
				nmos #2 (y[a], i[b], l[SIZE-(a-b)]);  
			end  
		end 
	endgenerate  
endmodule
```

![Lecture 7 详细建模-1746272654122.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272654122.png)

### 7.1.5 开关级存储器单元
- 半寄存器
```Verilog
module half_reg (input d, c, output q_bar);  
	supply0 Gnd;  
	supply1 Vdd;  
	trireg #(0, 0, 50) cap; //rise time; fall time; charge time delay
	cmos #(0,0,5) (cap, d, c, ~c);  
	nmos #(3) (q_bar, Gnd, cap);  
	pmos #(4) (q_bar, Vdd, cap);  
endmodule
```

![Lecture 7 详细建模-1746272659209.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272659209.png)

`58 = 5+50+3`
![Lecture 7 详细建模-1746272662845.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272662845.png)

- 伪静态的 D 锁存器
```Verilog
module d_latch (input d, c, output q);  
	cmos #(0,0,3)  
		(im1, d, c, ~c),  
		(im1, q, ~c, c);  
	not #(5)  
		(q_not, im1),  
		(q, q_not);  
endmodule
```
![Lecture 7 详细建模-1746272676201.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272676201.png)
- 交互耦合的 SRAM
```Verilog
module cross_couple (input d, wr, rd, output q_bar);  
	wire #(5) q_not;  
	wire #(3) im1, q_bar;
	
	nmos  
		(im1, d, wr),  
		(q_bar, q_not, rd);  
	not (q_not, im1);  
	not (pull0, pull1) (im1, q_not);  
endmodule
```
![Lecture 7 详细建模-1746272681009.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272681009.png)

![Lecture 7 详细建模-1746272691200.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272691200.png)
- 动态存储单元
```Verilog
module dynamic_cell (inout io, input rd_wr);  
	trireg #(0, 0, 50) cap;  
	tranifl #(5) (cap, io, rd_wr);  
endmodule
```
![Lecture 7 详细建模-1746272695066.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272695066.png)
- 主从触发器
```Verilog
module master_slave_diff (input d, c, output q);  
	half_reg master ( d, c, im1 );  
	half_reg slave ( im1, c_not, q );  
	not ( c_not, c );  
endmodule
```
![Lecture 7 详细建模-1746272699107.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272699107.png)

![Lecture 7 详细建模-1746272704217.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272704217.png)
- 如果触发器在相当长一段时间内没有受到时钟触发，它的数据就会丢失

## 7.2 强度建模
### 7.2.1 强度值
- 门电路输出和线网的默认强度是 `strong0` 和 `strong1`
- 电荷强度值为 `large, medium, small`
- 强度值倒序排列，`supply0` 和 `hignz0` 分别是逻辑 0 对应的最强和最弱的强度值
![Lecture 7 详细建模-1746272708986.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272708986.png)
- 线网强度
	- `drive_strength`：用于 `wire` 和 `tri` 类型线网
	- `charge_strength`：用于 `trireg` 存储类型线网
![Lecture 7 详细建模-1746272714847.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272714847.png)
### 7.2.2 决策使用的强度
- 逻辑强度用于在多个驱动源中选择信号的逻辑值
- 产生多驱动的缓冲器
```Verilog
module wired_strength (input a, b, output z1, z2, z3);  
	wire z1;  
	wand z2;  
	wor z3;
	// Wired logic  
	buf (pull1, weak0) (z1, a);  
	buf (pull1, weak0) (z1, b);
	// Wired - and logic  
	buf (pull1, weak0) (z2, a);  
	buf (pull1, weak0) (z2, b);
	// Wired - or logic  
	buf (pull1, weak0) a3 (z3, a);  
	buf (pull1, weak0) b3 (z3, b);  
endmodule
```
![Lecture 7 详细建模-1746272724565.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272724565.png)
- 输出 `z1,z2,z3` 线网类型不同，但采用不同的线网强度，均被上拉为 1
![Lecture 7 详细建模-1746272734013.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272734013.png)
- 输出 `z1,z2,z3` 线网类型不同，但采用相同的线网强度，输出取决于线网类型
```Verilog
module wired_strength (input a, b, output z1, z2, z3);  
	wire z1;  
	wand z2;  
	wor z3;
	// Wired logic  
	buf (pull1, pull0) (z1, a);  
	buf (pull1, pull0) (z1, b);
	// Wired - and logic  
	buf (pull1, pull0) (z2, a);  
	buf (pull1, pull0) (z2, b);
	// Wired - or logic  
	buf (pull1, pull0) (z3, a);  
	buf (pull1, pull0) (z3, b);  
endmodule
```
![Lecture 7 详细建模-1746289104700.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746289104700.png)
### 7.2.3 强度衰减
- 由于没有专门对开关的输出强度进行定义，因此，对不同类型的开关，输入强度传递到输出时，其强度会随着开关的类型发生变化
- `nmos,pmos,cmos，tran,tranif1,tranif0` 把输入送到输出只把 `supply` 降低为 `strong`
- 阻抗开关 `rnmos,rpmos,rcmos,rtran,rtranif1,rtranif0` 把输入的强度 `supply` 降为 `pull`，其它输入强度降一级
```Verilog
//采用无阻抗开关的MOS电路
module mos_strength (a, c, zn, zp);  
	input a, c;  
	output zn, zp;  
	nmos (zn, a, c);  
	pmos (zp, a, c);  
endmodule
```
![Lecture 7 详细建模-1746272744891.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746272744891.png)
```Verilog
module test_mos_strength;  
	reg a, c;  
	wire zn, zp;  
	mos_strength cut (a, c, zn, zp);
	
	initial begin  
		#10 a = 1;  
		#10 c = 0;  
		#10 a = 0;  
		#10 c = 1;  
		#10 a = 1;  
		#10 c = 0;  
		#10 $stop;  
	end
	
	initial  
		$monitor ("At time %t zn: %v, zp: %v", $time, zn, zp);  
endmodule
```
![Lecture 7 详细建模-1746289253983.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746289253983.png)

```Verilog
//采样阻抗开关的MOS电路
module mos_strength (a, c, zn, zp);  
	input a, c;  
	output zn, zp;  
	rnmos (zn, a, c);  
	rpmos (zp, a, c);  
endmodule
```
![Lecture 7 详细建模-1746289249213.png](/img/user/attachment_manager/Lecture%207%20%E8%AF%A6%E7%BB%86%E5%BB%BA%E6%A8%A1-1746289249213.png)