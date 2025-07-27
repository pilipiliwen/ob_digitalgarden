---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 1 基于Verilog的数字系统设计自动化/"}
---


# 1. 基于Verilog的数字系统设计自动化

## 1.1 数字设计流程

![Pasted image 20250329001552.png](/img/user/attachment_manager/Pasted%20image%2020250329001552.png)

### 1.1.1 设计输入

- 寄存器传输级 ( RTL: Register Transfer Level )
	描述数据在总线上、寄存器与寄存器之间的传递

#### 过程说明 (procedural statement)
	Similar to process in software,具有时序
	`initial, always`

####  连续赋值 (continuous assignment)
	Representing logic blocks, bus/wire interconnecting
	`assign  a=b`

#### 例化说明 (instantiation statement)：
	Using lower-level components in an upper-level design
	`DFFa DUT (..);`
{ #925364}


### 1.1.2 Verilog中的测试平台

[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 2 Verilog的寄存器传输级设计#2.4 测试平台\|Lecture 2 Verilog的寄存器传输级设计#2.4 测试平台]]
- 通过例化调用被用来仿真的模块

- Testbench 与设计模块共同组成了一个可被 Verilog 仿真程序仿真的完整模型

### 1.1.3 设计确认

#### 1.1.3.1 仿真 (前仿真)

又被称为行为级仿真、RTL 仿真或前仿真，是 ideal case

- No timing information ( setup and hold timing ), no hazards, no glitches
- Simulation fast
- Key purpose is to function verification
****
![Pasted image 20250329221036.png](/img/user/attachment_manager/Pasted%20image%2020250329221036.png)

![Pasted image 20250329221114.png](/img/user/attachment_manager/Pasted%20image%2020250329221114.png)
#### 1.1.3.2 断言验证

把断言仿真设计中，由设计者来决定是否满足工作条件，从而判断设计是否正确。这些工作条件称为设计特性，由设计者开发的断言检查来检查电路。

#### 1.1.3.3 形式验证

形式验证工具负责检查电路在任何情况下都能遵守设计者缺的的一些性能。

### 1.1.4 编译和综合 (Synthesis)

综合是从语言描述到对应的硬件模型的硬件自动生成过程。

- Step 1: Translating. Translate RTL code to generic Boolean format
- Step2 : Mapping (binding) and optimization
	---Target dependent : FPGA / ASIC /CPLD/IC

![Pasted image 20250329222336.png](/img/user/attachment_manager/Pasted%20image%2020250329222336.png)
#### 1.1.4.1 分析

输出分析结果，同时也检查了 Verilog 设计代码的语法

#### 1.1.4.2 硬件模型的生成

#### 1.1.4.3 逻辑优化

#### 1.1.4.4 绑定

#### 1.1.4.5 布局布线
![Pasted image 20250329223836.png](/img/user/attachment_manager/Pasted%20image%2020250329223836.png)

### 1.1.5 综合后仿真 

- ***Postsynthesis simulation (for FPGA/CPLD)***

- ***Post-layout simulation (for ASIC)***
	- Simulation speed is very slow. critical case only
	- Wiring delay, load effect, gate delay,
	- Process variation
	- Temperature variation
	- Supply Voltage Variation

- *Timing checking (for ASIC)*
	-  STA : static timing analysis,  efficient timing check

### 1.1.6 时序分析

工具分析并报告该电路在最坏情况下的时间延迟、时钟频率和门级延迟，以及电路应满足的建立和保持时间。

### 1.1.7 生成硬件电路

- 制造 ASIC 芯片所需的网表
- 可编程器件所需的下载文件
- 定制集成电路版图

## 1.2 Verilog 硬件描述语言

- 开关级
- 门级
- 引脚到引脚延迟
- 总线规格
- 行为级：类似于软件的语法结构
- 系统工具
- 可编程语音接口：PLI



