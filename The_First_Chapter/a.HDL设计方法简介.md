## 聊一聊HDL(硬件描述语言)发展历史：

#### 1.数字电路的发展：

- 数字集成电路经历了由最开始的电子管到现在的晶体管的过程。(当今大规模使用的是MOS管，场效应晶体管)
- 数字集成电路的规模经历了由 ``中小规模集成电路`` 到 ``VLSIC(超大规模集成电路)`` 到 ``ASIC(专用集成电路)``。
  - 其中，集成电路的英文是：``specific integrated circuit``。
- 数字逻辑器件经历了由``逻辑门``到现在的复杂的``Soc(片上系统)``。

#### 2.数字电路的设计历史：

- 早期``(手撕)``：由于早期的``EDA``应用有限，工程师们利用``卡诺图``手撕CMOS的开关电路或者门级电路，通过面包板等实验系统验证。这种方式的设计工作量非常的大，并且设计的电路规模大不了。
- 中期``(原理图)``：中期采用的设计方式``原理图输入``，logisim这类工具就是原理图输入。直接在EDA中根据要求选用元器件库中的元器件进行逻辑连接，优点是非常直观``(毕竟是连线)``，缺点是可维护性非常的差，当芯片升级换代后，基本上整个原理图都要做改正，而不仅仅是换一个芯片那么简单。
- 后期``(HDL)``：后期采用的是``硬件描述语言``。
  - 硬件描述语言自顶向下分为：``系统级System Level``、``行为级Behavioral Level``、``寄存器传输级RTL``、``门级Gate Level``。
  - 我们使用HDL设计的时候指考虑设计层次较高的部分，然后把设计好的部分交给EDA，EDA工具会自动将高层次的电路描述为门级``(这个过程叫做逻辑综合)``，这大大减少了设计时间。
  - 但是，并不是我们在设计的时候电路的层次越高越好。我们现在最成熟的EDA工具是RTL级的电路转换为门级网表``(门级网表指的是基本逻辑门组成的逻辑连接)``，因此我们在设计的时候一般设计RTL级电路。

#### 3.Verilog HDL与VHDL：

- Verilog HDL适合描述``RTL级``的电路，并且该语言设计电路时只依靠寄存器和线网类型，时序与组合电路描述简介，是容易上手的一门硬件描述语言。
- VHDL适合描述``System级 or Behavioral级``的电路，该语言不适合初学者入门，并且该语言正在被淘汰。

#### 4.Verilog HDL与C语言：

- 相同点：
  - Verilog HDL是根据C语言发明而来的，具备了C语言简单易用的特点，并且很多语法都是从C语言处借鉴而来。
- 不同点：
  - 我们可以说主要由三点不同，并且正是这三点，使得Verilog HDL与C语言有着本质的区别：
    - ``互连``：Verilog通过线网变量将不同模块连接起来，而C语言没有将不同模块连接起来的变量。
    - ``并行``：Verilog语言是描述硬件电路的语言，因此``并行性``是其最主要的特点，硬件系统中，各个运算单元的运算是独立进行的，``信号流是并行的``。而C语言在经过编译后，机器指令在CPU的高速缓冲队列中基本上是顺序执行。
    - ``时间``：Verilog在描述硬件电路的时候有着``周期``的概念，而C语言没有时间概念。
- 结合使用：
  - PLI``(Program Language Interface)编程语言接口``，是Verilog与C语言的接口。
  - 通俗来讲，就是使用一组符合PLI的C语言函数，编写一个Verilog的仿真程序。
  - 由于仿真程序的设计层次较高``(仿真一般都属于系统级)``，这样做可以弥补Verilog语言高层次建模的缺陷。

#### 5.Verilog HDL电路的本质：

- Verilog HDL电路本质上是硬件描述语言，是描述硬件的，注意是描述，而不是编写，所以我们在设计的时候心里要有电路！