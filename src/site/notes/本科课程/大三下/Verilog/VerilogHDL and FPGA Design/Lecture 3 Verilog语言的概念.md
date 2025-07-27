---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 3 Verilog语言的概念/"}
---


# 3. Verilog 语言的概念
## 3.1 硬件描述语言的特征
### 3.1.1 时序性（Timing）

- 需要考虑门延时、线延时、毛刺......
- 在连续赋值语句中使用 `#2` 表示延时 2 ns

### 3.1.2 并发性（Concurrency）

- 硬件系统中的每个元件都在并行工作
- Verilog允许过程块中使用过程语句：`if-else ；case
- CPU或处理器顺序处理代码，让用户认为这样的执行是并发完成的
- Verilog并发体中语句的顺序并不重要
- `always` 块内部的语句都是顺序执行的，但是从 `alway` 块外部看起来，`always` 块是一个并发执行的结构体。

## 3.2 模块基础知识
### 3.2.1 代码格式

- 空格和回车作为分隔符
- 区分大小写
- 关键词都采用小写字母
- `//` 和 `/*     */` 用于注释

### 3.2.2 逻辑值系统

- 0：直接接地的逻辑值
- 1：电源电压驱动的逻辑值
- X：多驱动值的冲突
- Z：未被驱动的高阻值

![Lecture 3 Verilog语言的概念-1743338923868.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743338923868.png)

### 3.2.3 数据类型
网线和变量是 Verilog 中的两种主要数据类型
- `net` 表示硬件结构驱动的连线或者门电路的输出
- 在 Verilog 过程块中的一个元件进行行为描述时， `reg` 表示用来保存数据的变量
#### 3.2.3.1 `net` 声明
- `net` 表示硬件结构驱动的连线或者门电路的输出

![Lecture 3 Verilog语言的概念-1744478767202.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744478767202.png)
![Lecture 3 Verilog语言的概念-1744478882120.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744478882120.png)
![Lecture 3 Verilog语言的概念-1744478886156.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744478886156.png)
#### 3.2.3.2 `reg` 声明
- `reg` 表示用来保存数据的变量
- `reg` 类型有 `integer` 和 `time`, `integer` 声明了有符号的补码形式的整数，`time` 声明了位宽至少 64 bit 的无符号 reg 变量

#### 3.2.3.3 有符号数
- `reg` 和 `net` 类型都可以被声明为有符号数

#### 3.2.3.4 参数
- 参数是常数，它的值在运行过程中不能改变

![Lecture 3 Verilog语言的概念-1744479390114.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744479390114.png)

### 3.2.4 模块

- 模块以 `module` 开始，`endmodule` 结尾
- 硬件模块有端口定义；testbench 的模块没有任何端口

- 模块的输入和输出可以声明为：`input, output或inout`
- 端口的默认属性为：`wire型的net`
- 声明为 `output` 的端口可以同时声明为 `reg` 类型 
- 可以通过连续赋值语句将 `reg` 型变量的值送给 inout 端口

### 3.2.5 数字

```Verilog
- number_of_bits' s base_identifier digits
	- number_of_bits 位宽，可以不指定
	- s 表示是个有符号数，可以不指定
	- base_identifier 基标识符：用 b、d、o和h 来表示二、十、八、十六进制
- 可以再数字的中间任意使用下划线 _ 将数字隔开
```
![Lecture 3 Verilog语言的概念-1744462432308.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744462432308.png)

### 3.2.6 数组

- `reg [7:0] a_array [0:1023][0:511];` 表示一个 8 比特的二维数组

-  `reg [7:0] Areg;` 表示一个8比特的寄存器，不能单独修改其中一位的值

- `reg Amem [7:0]` 表示一个1比特8个地址的数组，可以修改每个地址的值

![Lecture 3 Verilog语言的概念-1743341263431.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743341263431.png)
```Verilog
reg [7:0] Areg;   
reg Amem [7:0];
reg Dmem [7:0][0:3];
reg [7:0] Cmem [0:3];
reg [2:0] Dmem [0:3][0:4];
```
#### 3.2.6.1 比特选择
比特选择用包含在方括号中的比特地址循环来选定某个比特
```Verilog
Areg[5] //selects bit 5 of the Areg array.
```
#### 3.2.6.2 部分比特选择
```Verilog
Areg [5:3]//selects bits 5, 4, and 3
Areg [5-:4] //selects bits 5, 4, 3, and 2
Areg [2+:4] //selects bits 5, 4, 3, and 2
```

#### 3.2.6.3 标准存储器
```Verilog
reg [7:0] Emem [0:1023]

Cmem [AREG[7:6]]
Emem [Emem[0]]
Emem [355] [3:0] //存储器中的355个字节单元中的低4比特
Emem [355] [3-:4] //存储器中的355个字节单元中从比特3开始的4个比特，递减顺序
//Emem [355:358] 是错误的，不支持指定某个存储器的地址范围
```
#### 3.2.6.4 多维存储器的序号
![Lecture 3 Verilog语言的概念-1744480252975.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744480252975.png)

### 3.2.7 运算符
#### 3.2.7.1 基本运算符
![Lecture 3 Verilog语言的概念-1743341452113.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743341452113.png)

- `2**4` = $2^4$
- 若两个操作数中包括 X 或 Z，乘法结果整个输出为 X
- %取模运算符：第一操作数被第二操作数除后得余数, 符号来自第一个操作数
- 关系运算符任意一个操作数是 X 或 Z，则输出结果会变为 X
- 位宽不一样的操作数比较时，位宽较小的操作数将会在其左边自动补 0

![Lecture 3 Verilog语言的概念-1744470244741.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744470244741.png)
#### 3.2.7.2 相等运算符
- 逻辑相等操作符中包括 X 或 Z，输出为 X
- 逻辑运算符不包括高阻和不定态，情形运算符包括

![Lecture 3 Verilog语言的概念-1744470259887.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744470259887.png)
#### 3.2.7.3 布尔运算符
![Lecture 3 Verilog语言的概念-1743341458462.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743341458462.png)

- 逻辑布尔运算：
	- 当所有操作数都非 0 时， `&&` 运算符的返回结果为 1
	- 当所有操作数有一个非 0 时，`||` 运算符的返回结果是 1
- 比特运算符（左下 1 是 X）：
- `&`：AND，`|`： OR，`^:` XOR, `^~:` XNOR, `~:` NOT
- `^` 异或
- `^~`, `~^` 同或

![Lecture 3 Verilog语言的概念-1743352020528.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743352020528.png)
- 缩位运算符：
	- 将该向量操作数按比特逐位进行运算，产生 1 比特的结果

![Lecture 3 Verilog语言的概念-1744470301965.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744470301965.png)

```Verilog
a = 0110;
b = 0111;
//逻辑运算符
c = a||b; // c = 1
//比特运算符
d = a|b; // d = 0111
//缩位运算符
e = |a; // e = 1
f = |b; // f = 1
```



#### 3.2.7.4 移位运算符
- 逻辑移位在移位后空出的位置补 0
- 算数运算符对于无符号数操作与逻辑移位一致；对于有符号数，算数移位运算符在右移时，运算符左边的操作数的 MSB 将填充移位后留下的空白。

![Lecture 3 Verilog语言的概念-1744470904735.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744470904735.png)
#### 3.2.7.5 并置运算符
![Lecture 3 Verilog语言的概念-1743341747694.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1743341747694.png)
- 利用并置运算符，用位宽较小的矢量或标量来表示新的矢量，广泛应用于硬件建模
```Verilog
{a,aa} = 10'b1101001001
aaa = {aa, {2{a}}, 2'b11}
{a, 2{b,c}, 3{d}} 等价于 {a,b,c,b,c,d,d,d}
{2'b00, 3{2'01}, 2'b'11}的结果是10'b0001010111
```
#### 3.7.5.6 条件运算符
```Verilog
表达式1？表达式2：表达式3
if-then-else
如果表达式1的值是X或Z，则将计算表达式2和表达式3，其输出结果为两个表达式对应比特的组合，如果对应比特都为0，则结果为0；如果对应比特都为1，则结果为1;除此之外的情况都会产生X。
```
![Lecture 3 Verilog语言的概念-1744478330690.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744478330690.png)
#### 3.7.5.7 运算符的优先级
- 小括号优先级最高
- 运算符从左至右的顺序关联，条件运算符从右至左
![Lecture 3 Verilog语言的概念-1744478521131.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744478521131.png)

## 3.3 Verilog 仿真模型
### 3.3.1 连续赋值语句

- 连续赋值语句仅用于并发结构
```Verilog
并发的连续赋值语句：
module Mux2to1 (input a,b,c, output w);
	wire n,m,p;
	assign #3 w = m | p;
	assign #3 p = n & c;
	assign #6 n = ~b;
	assign #2 w = m | p;
endmodule

使用net_decleration_assignment结构:

module Mux2to1net (input a,b,c, output w);
	wire #3
		m = a & b,
		p = n & c,
		n = ~b,
		w = m | p;
endmodule


```
- 强度描述
```Verilog
assign （strong0, strong1） w = m | p;
```
![Lecture 3 Verilog语言的概念-1744517077510.png](/img/user/attachment_manager/Lecture%203%20Verilog%E8%AF%AD%E8%A8%80%E7%9A%84%E6%A6%82%E5%BF%B5-1744517077510.png)

### 3.3.2 过程赋值语句
- 两种结构化的过程语句：`initial  always`
- 对 `reg` 类型数据的赋值发生在过程体中

#### 3.3.2.1 阻塞和非阻塞语句
- 阻塞赋值语句：中间用等号 `=` 相连 
- 非阻塞赋值语句：中间用左箭头符号 `<=` 相连，当程序执行到非阻塞赋值语句时，计算语句右边的值，一旦满足内嵌赋值控制，就将它赋给左边的 `reg` 类型数据
- 内嵌赋值语句：出现在过程赋值语句等号的右边
```Verilog
initial begin : Blocking_Assignment_to_b
	b = 1;
	#100
	b = #80 0;
	b = #120 1;
	#100
end
//在180s，300s变换

initial begin : Nonblocking_Assignment_to_a
	a <= 1;
	#100
	a <= #80 0;
	a <= #120 1;
	#100
end	
//在180s，220s变换
```
#### 3.3.2.2 多个赋值语句
```Verilog
initial begin
	clk = 0;
end

always begin
	#0;
	clk = ~clk;
	#17;
end
//考虑0时刻，initial和alway语句同时执行，需要让alway语句晚于initial语句初始化
```
#### 3.3.2.3 过程连续赋值语句
- 过程连续赋值语句度 `reg` 变量赋值后，其余赋值语句不能再对该变量赋值
`assign qout <= 0`
- 只要不过对 qout 解赋值，其它赋值语句则无法影响该值，解赋值：
`deassign qout；`

```Verilog
module FlipflopAssign (input reset, din, clk, output qout);
	reg qout;
	alway @ (reset) begin
		if(reset) assign qout <= 0;
		else deassign qout;
	end
	always @ (posedge clk) begin
		qout <= din;
	end
endmodule
```
#### 3.3.2.4 强加与释放
- 过程连续赋值语句的另一种形式
- `force` 和 `release` 语句不仅可用于 `reg` 类型变量，也可用于 `net` 类型变量

## 3.4 编译指令（Compiler Directives）
### 3.4.1 `timescale

```Verilog
`timescale 1ns/100ps
把与时间相关的所有数字的时间单位标注为1ns，当表达式计算时间值时，其精度为100ps。
```

### 3.4.2 `default_nettype

```Verilog
网线如果未定义，则隐含为wire类型
`default_nettype wor
修改默认类型为 wor（线或）；wand（线与）
这个默认值会一直有效，除非将它设置为另一个值或用指令`resetall复位
```

### 3.4.3 `include

```
在模块里包含参数定义文件或部分共享代码
```

### 3.4.4 `define

```Verilog
`define word_lengh 32
`define begin_fetch_state 3'b101
	
可以再Verilog代码任意位置使用`begin_fetch_state代表101
指令`undef取消对前一个文本宏的定义
```
### 3.4.5 `ifdef,else,和endif
```Verilog
`ifdef检查是否被`define定义过
如果定义过：编译`ifdef和`else扩住的语句
如何没有定义过：编译`else和`endif扩住的语句
```
### 3.4.6 `unconnected_drive
```Verilog
将未连接的端口值设置为pull0或pull1
可用`nounconnected_drive将指令`unconnected_drive的指定取消
```
### 3.4.7 `celldefine endcelldefine
```Verilog
指令对`celldefine和`endcelldefine把扩住的模块当成一个单元
for library modelling
```
### 3.4.8 `resetall
```Verilog
取消所有编译指令的指定内容
```
## 3.5 系统任务和函数
``` Verilog
显示任务
$monitor
$display：$displayb; $displayh; $displayo

文件I\O任务
$fopen
$swrite, $fread and $readmemh....

时间刻度任务
$printtimescale
$timeformat

仿真控制任务
$finish
$stop

时序检查任务
$nochange (posedge clock, d_input, 3, 5);
$setuphold …

实数转化函数
$bistoreal
$realtobits
$itor
$reoi

其它
$random
$realtime
$time
$stime
```












