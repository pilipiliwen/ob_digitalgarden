---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 2 Verilog的寄存器传输级设计/"}
---


# 2. Verilog的寄存器传输级设计

## 2.1 寄存器传输级设计

RTL: 将负责设计分解成几个小模块，用总线将其链接搭建起来，然后再描述和实现这些小模块的设计方法。

- Small Design: Directly RTL coding
- Large Design: Planning, architecture, partition

### 2.1.1 控制部分和数据部分的划分

- Data part: 数据单元、总线结构

- Control part: 产生控制信号的状态机

![Pasted image 20250329232121.png](/img/user/attachment_manager/Pasted%20image%2020250329232121.png)
#### 2.1.2 数据部分

- 数据单元
	- 寄存器
	- 组合逻辑单元
	- 寄存器阵列
	- 连接它们的总线

![Pasted image 20250329232609.png](/img/user/attachment_manager/Pasted%20image%2020250329232609.png)
![Pasted image 20250329232941.png](/img/user/attachment_manager/Pasted%20image%2020250329232941.png)

#### 2.1.3 控制部分

- 负责处理来自数据部分和外部的控制信号
- 通过判断当前数据的状态来决定操作

![Pasted image 20250329232805.png](/img/user/attachment_manager/Pasted%20image%2020250329232805.png)

## 2.2 Verilog 基础

### 2.2.1 硬件模块

- 模块由关键字 `module` 声明

### 2.2.2 原语例化
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 4 组合电路描述#4.2.1 门原语\|Lecture 4 组合电路描述#4.2.1 门原语]]
Verilog 最基本的逻辑门成为原语 (Primitive)，或称为基元。为了使用这些原语，需要用到原语例化的结构。
![Pasted image 20250330125447.png](/img/user/attachment_manager/Pasted%20image%2020250330125447.png)
![Pasted image 20250330125451.png](/img/user/attachment_manager/Pasted%20image%2020250330125451.png)

### 2.2.3 连续赋值

- 用布尔表达式描述逻辑关系

![Pasted image 20250330125627.png](/img/user/attachment_manager/Pasted%20image%2020250330125627.png)

### 2.2.4 条件表达式

- If-then-else

![Pasted image 20250330125730.png](/img/user/attachment_manager/Pasted%20image%2020250330125730.png)
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 3 Verilog语言的概念#3.7.5.6 条件运算符\|Lecture 3 Verilog语言的概念#3.7.5.6 条件运算符]]
[[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 4 组合电路描述#4.4.4 条件运算\|Lecture 4 组合电路描述#4.4.4 条件运算]]
### 2.2.5 过程块

使用层次更高的过程结构：`always`
![Pasted image 20250330130032.png](/img/user/attachment_manager/Pasted%20image%2020250330130032.png)

### 2.2.6 模块例化

- 先定义子模块，再把子模块连接，以描述一个更高层次的设计

![Pasted image 20250330130157.png](/img/user/attachment_manager/Pasted%20image%2020250330130157.png)
![Pasted image 20250330130203.png](/img/user/attachment_manager/Pasted%20image%2020250330130203.png)

## 2.3 Verilog 中的元件描述
### 2.3.1 数据元件
#### 2.3.1.1 多路选择器

- 8 比特位宽的 2 选 1 多路选择器

![Lecture 2 Verilog的寄存器传输级设计-1743311741663.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743311741663.png)

#### 2.3.1.2 触发器

- 触发器用在需要标志位的数据通道和数据存储设计中

![Lecture 2 Verilog的寄存器传输级设计-1743312166735.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743312166735.png)

#### 2.3.1.3 计数器

- 4 比特、模 16 计数器

![Lecture 2 Verilog的寄存器传输级设计-1743312357150.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743312357150.png)

#### 2.3.1.4 全加器

![Lecture 2 Verilog的寄存器传输级设计-1743312518566.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743312518566.png)

#### 2.3.1.5 移位寄存器
- 当 m=1，位移寄存器右移
- 当 m=2，位移寄存器左移

![Lecture 2 Verilog的寄存器传输级设计-1743313040075.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743313040075.png)

#### 2.3.1.6 算术逻辑单元

![Lecture 2 Verilog的寄存器传输级设计-1743313371486.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743313371486.png)

#### 2.3.1.7 互连线

ALU 8 和 Mux 8 被例化为 U 1 何 U 2 两个元件，通过互连线连接

![Lecture 2 Verilog的寄存器传输级设计-1743313545965.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743313545965.png)

![Lecture 2 Verilog的寄存器传输级设计-1743313540602.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743313540602.png)

![Lecture 2 Verilog的寄存器传输级设计-1743313560091.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743313560091.png)

### 2.3.2 控制器
#### 2.3.2.1 同步器
同步器将输入数据与想要的时钟同步
![Lecture 2 Verilog的寄存器传输级设计-1743314374496.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314374496.png)

![Lecture 2 Verilog的寄存器传输级设计-1743314380735.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314380735.png)

#### 2.3.2.2 序列检测器

![Lecture 2 Verilog的寄存器传输级设计-1743314435658.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314435658.png)

- Moore 状态机检测输入序列 a 中是否存在 110 序列的电路
-  [[本科课程/大三下/Verilog/VerilogHDL and FPGA Design/Lecture 5 时序电路描述#5.4 状态机编码\|Lecture 5 时序电路描述#5.4 状态机编码]]
![Lecture 2 Verilog的寄存器传输级设计-1743314438865.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314438865.png)

![Lecture 2 Verilog的寄存器传输级设计-1743314585934.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314585934.png)

## 2.4 测试平台

- Testbench 也用 `module` 关键字开头，但不需要输入输出端口

![Lecture 2 Verilog的寄存器传输级设计-1743314781896.png](/img/user/attachment_manager/Lecture%202%20Verilog%E7%9A%84%E5%AF%84%E5%AD%98%E5%99%A8%E4%BC%A0%E8%BE%93%E7%BA%A7%E8%AE%BE%E8%AE%A1-1743314781896.png)
