## 从整体的视角来看Verilog HDL：

### 一、Verilog HDL的开发流程：

- #### 1.系统级描述：System Description

  - 从宏观的角度设计电路，也就是分析要实现的功能，然后把整体的电路拆分成一个一个的模块，我们后续使用Verilog HDL语言描述的就是这些模块。

- #### 2.行为级描述：Behavioral Description

  - 在Verilog HDL中，行为级的描述全部为``仿真测试``，这里的仿真包括``RTL级电路的仿真``、``门级电路的仿真``、``布局布线后的仿真``，这三种仿真测试全部都属于行为级描述。

- #### 3.RTL级描述：

  - RTL级描述是Verilog HDL中最重要的一部分，这一部分的描述是真正形成电路的部分。对RTL级电路的描述需要使用Verilog中的可综合语言，可综合语言是其中的一个很小的子集，因此使用Verilog来描述电路准确且简单。

- #### 4.RTL级仿真(功能仿真)：RTL Simulation

  - 当我们写好RTL级电路之后，需要进行RTL级电路的仿真操作，以确定所设计的RTL级电路有没有问题。

- #### 5.逻辑综合——门级：Logic Synthesis

  - 当我们描述完RTL级电路之后，通过逻辑综合工具``(这里的综合工具指的是RTL级综合工具)``，把对应的RTL电路转换为门级网表。

- #### 6.门级仿真：Gate Simulation

  - 当电路综合成门级网表之后，再次进行仿真测试，检测电路转换后有没有问题。
  - 一般情况，门级仿真可以省略，门级仿真与RTL级仿真的测试激励相同。

- #### 7.布局规划或布局布线：Placement & Routing

  - 布局指的是：将电路器件安装在芯片上。
  - 布线指的是：将芯片上的电路器件进行连接。

### 二、Verilog HDL在开发中的使用(作用)：
##### 1.描述电路：
- 使用verilog对电路进行描述的时候，必须使用verilog的可综合语句，只有可综合语句才能进行后序的逻辑综合的操作。
- 可综合语句在verilog的语法中占比非常非常小。
- 可综合语句是真正设计电路芯片的部分。
##### 2.构建仿真：
- verilog剩下的语法中的一部分来构建测试环境，用来产生电路工作时所需要的信号，由于该部分只是用来检验设计的电路的逻辑性，不会参与接下来的步骤，因此只要符合语法逻辑就行。
### 三、Verilog HDL语言结构：
- 我们通过一个verilog的模块示例来了解一下verilog的语言结构：
```verilog
module block(in1,in2,out0)
input in1,in2;
output[1:0] out0;

reg[1:0] out0;

always@(in1 or in2)
begin
......
end
endmodule
```
##### 1.模块化思维：
- Verilog HDL中的设计思想是模块化的设计思想，即把要实现的逻辑放入一个模块中。
- 每一个模块都写在``module ... endmodule``之间。
- 上面的模块定义了一个组合逻辑的与门，模块名称为block。
##### 2.定义端口：

- 模块名称后面的括号叫做：端口列表。

- 端口列表里面要写该模块中用到的所有输入、输出、双向端口，并且在模块内部将这些端口声明出来。
##### 3.声明信号类型：
- 声明输入输出信号的类型：把out0定义为reg类型的信号，规定位宽为2bit，最低为编号0，最高位编号1。
  - 注意，reg类型在定义端口的时候就要定义为2bit。
- 对于in1和in2这两个信号，没有对其声明类型，因此默认为wire类型，位宽为1位。
  - 在模块中，如果某个端口信号没有进行类型声明的话，该信号默认为wire类型，位宽为1bit。
###### 重点：
- 除了信号端口需要声明之外，对于电路中所需要的连线等变量同样需要声明类型。
##### 4.功能描述：
- 使用always或者assign语句对电路的功能进行描述。
- 逻辑电路与逻辑功能相互等价。
### 五、Verilog HDL的端口声明风格：
##### 1.Verilog-1995：
```verilog
module block(in1 , out1)
	input in1;
	output out1;
	/*
	把I/O端口定义在模块里面，而模块名后的括号内写所有端口的名称。
	*/
endmodule
```
##### 2.Verilog-2001：
```verilog
module block(input in1 , output out1)
	/*
	把I/O端口直接定义在模块名后面的括号中。
	*/
```
##### 3.选择：
- 由于模块中的I/O端口可能较多，全放在括号里面的话降低可读性，因此选择1995版本比较合适。