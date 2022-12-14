## Set up Time & Hold Time & Metastable

### a.同步信号与异步信号：

- 同步信号分为以下三种：
  - 1.a、b两个信号是``同频同相``的，这里的同频指的是``频率``相同，也就是周期相同；同相指的是``相位``相同。
    - 相位相同：信号a的上升沿对应信号b的上升沿，信号a的下降沿对应信号b的下降沿，在频率相同的前提下，这两个信号的相位相同。
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/同步信号.jpg)
    - 上述所示的两个信号就是一对``同频同相``信号。
  - 2.a、b两个信号的周期相同，相位存在时间偏移。
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/同频但相位偏移.jpg)
    - 上述两个信号之间存在时间T的偏移量，但两者是同步信号。
  - 3.分频信号，频率之间存在着倍数关系。
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/分频信号.jpg)
    - 信号b和信号c都是信号a的二分频信号，即周期是信号a的二倍。
    - 信号a与信号b属于同步信号，信号a与信号c同样属于同步信号。
- 异步信号：
  - 异步信号指的是信号之间没有具体的对应关系的信号。

### 1.建立时间与保持时间：

- 建立时间和保持时间是相对于时钟信号来讲的，我们通过Verilog中的D触发器解释建立时间和保持时间：
  - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/D_flipflop.jpg)
  - 当时钟信号处于``上升沿``的时候，``CLK``首先会对数据``D``进行采样，然后将``D``的内容存储到``Q``端，其他时刻Q端的值保持稳定不变，这就是D触发器的功能。
  - 建立时间：
    - 从本质上来讲，D触发器是由一系列``逻辑门电路``构成的，逻辑门电路必然会产生``延时``。
    - 在D触发器内部，CLK对D采样的过程是通过逻辑门完成的，然而CLK信号和D信号经过的路径却不同，这里我们说明在D触发器内部，D信号经历的时间要比CLK信号经历的时间长。
    - 因此为了这两个信号能够稳定的进行逻辑操作，需要让D信号快人一步，也就是提前让D信号输入到触发器中，D信号领先CLK信号的时间我们就叫做``建立时间``，在``建立时间``中，时钟信号不能进入触发器，如果进入触发器，时钟信号就会先一步到达逻辑门处，此时与CLK进行逻辑操作的数据并不是我们期望的数据，因此会出现采样失败。
    - 注意，由于我们是对数据D进行采样，因此数据D可以和CLK同时到达逻辑门处，数据D也可以领先CLK到达逻辑门处，但是不能落后于CLK。
    - 通过上面的分析可以得出：D触发器的``建立时间``是**硬件构造**所决定(数据传输路径是固定的，建立时间就是传输路径上的延迟时间)的，因此**给定D触发器，就给定了建立时间**。
    - 既然建立时间是确定的，那么我们可以把``建立时间和时钟``进行挂钩，这样方便进行分析：
      - 时钟上升沿到来之前的一段时间是``建立时间``，数据要在建立时间之前保持稳定，这样能够成功采样。
      - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_12-12-45.jpg)
      - 对于第一个时钟来讲，数据信号不满足建立时间，经过一个触发器延迟之后，Q的输出端会产生一个不稳定值，即产生亚稳态``METASTABLE``。
      - 对于第二个时钟来讲，数据信号在建立时间内都保持稳定，因此满足建立时间。
  - 保持时间：
    - 当CLK信号对D信号``开始采样``之后，一直到将D信号发送到输出端Q，这一段时间相当于触发器内部的延迟时间，这段延迟时间就叫做保持时间。
    - 因此可以得出这样的结论：保持时间的大小由D触发器的内部构造决定，当D触发器固定的时候，保持时间就固定了。
    - 在这一段延迟时间内，包括CLK对D的采样时间以及D信号发送到Q端的时间。因此在这一段时间内，D信号的同样要保持稳定，不能更改。
    - 我们可以近似的把触发器的延时等价位保持时间。
    - 如果在保持时间内，D信号发生改变，那么输出端Q就会产生一个不稳定的值，即产生亚稳态``METASTABLE``。
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_12-48-51.jpg)
    - 上面所表示的波形图中，数据信号D的满足时钟的建立时间和保持时间，因此不会在输出端Q处输出不稳定值。

### 2.数据流传输中的Metastable：

- 使用触发器进行数据流传输是电路设计里面非常常见的手段，但是不可避免的会产生亚稳态，即数据信号D没有满足时钟的建立时间和保持时间。
- 这时，我们可以使用两个触发器进行串联，使用两个触发器相当于打一拍，当第一个触发器产生亚稳态的时候，第二个触发器可以大概率消除第一个触发器亚稳态的影响，从而产生正常的输出。
- ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_13-28-13.jpg)
  - 假设一开始Q1的输出为1，Q2的输出为0。
  - 此时时钟的上升沿到来，由于D没有遵循时钟的``保留时间和建立时间``，此时Q1产生亚稳态。但是此时Q1的亚稳态并不会影响到第二个触发器，因为两个触发器之间间隔一拍。(我们要注意，此时D2端的信号也是亚稳态，但是由于时钟有效沿没有到来，因此我们看到的Q2依旧是稳定的输出)。
  - 如果在第二个时钟周期上升沿到来之前，让Q1的输出收敛(即产生稳定的值，毕竟亚稳态的时间有限)，那么当时钟上升沿到来的时候，D2就会输入一个稳定的值(因为Q1在此之前已经稳定，亚稳态消失)，此时Q2依旧是正常的输出。
  - 这样就通过两个触发器大概率的避免了亚稳态的产生。
  - 但是并不会完全避免，只是大概率避免。

### 3.同步复位与异步复位：

- 同步复位电路：

  - 同步复位电路含有``同步复位触发器``，这个触发器同样是``边沿触发的D触发器``。
  - 原理图：
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/同步复位1.jpg)

  - "同步"的由来：
    - 对于该触发器来讲，控制信号只有CLK一个，并且只有在CLK的上升沿才会触发该触发器。因为只有一个控制信号，自然而然是同步的，又因为该触发器能够执行复位功能，因此叫同步复位电路。

  - 电路特点：
    - 时钟上升沿触发该触发器，如果此时D'信号为``低电平``，则执行复位操作，否则将D'的值存入输出端Q。

  - 亚稳态：
    - 我们在在上面讨论过``D触发器的亚稳态``，这个触发器同样由产生亚稳态的可能性，但是这种概率非常小，因此一般的资料认为同步复位电路不会产生亚稳态。
    - 亚稳态分析：
      - 如果D‘在时钟上升沿的建立时间和保持时间之内发生跳变，那么就会产生亚稳态。
      - 但是让D‘发生跳变的情况非常苛刻，并且还有与门的门延迟，因此亚稳态发生的概率非常小。

  - 双触发器的同步复位电路：
    - 为了进一步的降低亚稳态发生的可能性，我们采用双触发器的电路，如果第一个触发器发生亚稳态，那么第二个触发器可以高概率避免亚稳态的转移，从而使输出保持稳定。
    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/同步复位2.jpg)
    - 这种同步复位电路亚稳态发生的可能性几乎可以忽略不计。

  - 同步复位电路的优点：
    - 同步复位电路只有一个时钟信号进行控制，便于时序分析。
    - 同步复位电路产生亚稳态的可能性非常小。

  - 同步复位电路的缺点：
    - 同步复位电路一般没有同步复位信号端口，因此需要使用逻辑门将输入信号和复位信号进行逻辑运算之后送入触发器的输入端口，这样会占用更多的逻辑资源。
    - 同步复位电路的``复位信号的有效时间``(这里指的是低电平的有效时间)要大于``时钟周期``，只有这样才能确保**一定**能够产生复位操作。否则可能因为复位信号有效时间过短而不能产生复位。
    - 同步复位电路的复位操作不一定立刻执行，一般来讲会在复位信号生效后的下一个时钟上升沿执行复位操作。

- 异步复位电路：

  - 异步复位电路含有``异步复位触发器``，该触发器也是``边沿触发的D触发器``。

  - 原理图：

    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_19-18-19.jpg)

  - "异步"的由来：

    - 对于该触发器来讲，控制信号有CLK和rst两个信号，这两个信号一般不会同步，因此对于有两个``不同步信号``的电路，我们叫做``异步电路``，又因为该电路能够进行复位操作，因此我们叫做异步复位电路。

  - 电路特点：

    - 时钟上升沿和复位信号的下降沿都能够触发该触发器，并且复位信号的下降沿优先触发。
    - 当复位信号的下降沿到来的时候，无论其他信号是什么情况，都执行复位操作。**(查阅了很多资料，都没有说复位信号下降沿起作用的时候会产生亚稳态，可能跟电路的实现有关)**
    - 只有当``复位释放``(复位释放指复位信号由0变为1这一个过程，也就是``复位信号的上升沿``)后，遇到第一个时钟上升沿才会将信号D存入Q端口。

    - 亚稳态：
      - 关于异步复位电路，可能由于电路内部的设计，只有当复位信号上升沿和时钟的上升沿同时出现的时候，会在输出端产生亚稳态。
      - 分析：
        - 我们引入两个概念：``恢复时间Recovery Time`` & ``解除时间Removal Time``，这类似于建立时间和保持时间。
        - 触发器内部有由复位信号上升沿和时钟上升沿执行逻辑操作的部分，并且时钟上升沿的信号会优先到达，而复位信号的上升沿经历的延迟较大，因此我们需要一个恢复时间，**让复位信号的上升沿先执行，然后在执行时钟信号的上升沿**，这段时间就叫``恢复时间``(其实非常类似建立时间)。
        - 之后两个信号经历逻辑延迟，正确执行控制作用，这段时间叫做``解除时间``。

      - 当时钟信号的上升沿在复位信号的上升沿的``恢复时间和解除时间``内生效的时候，就会引起触发器内控制部分的亚稳态，进而输出也为亚稳态。

  - 异步复位电路的优点：

    - 大多数触发器都有异步信号端口，因此不需要占用额外的逻辑资源。
    - 当异步复位信号生效的时候，能够立刻执行复位操作，复位快。

  - 异步复位电路的缺点：

    - 对于异步电路来讲，这两个异步信号很可能就会相互作用产生亚稳态，因此在异步复位电路中，亚稳态的产生是严重的缺点。

  - 双触发器的异步复位电路：

    - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/异步复位2.jpg)
    - 使用双触发器可以减少亚稳态产生的概率。

- 异步复位同步释放电路：

  - 如果我们既想实现异步复位的功能，又想避免异步复位电路的亚稳态，可以使用异步复位、同步释放电路。
  - 该电路后面遇到再整理......


### 4.跨时钟域传输的Metastable：

- 同步器：
  - 同步器适合进行两个时钟域的时钟是**同步信号**的跨时钟域传输操作。
  - 同步器指的是多个触发器串联，这样可以最大限度的避免亚稳态的产生。
  - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_18-13-35.jpg)
  - 由于CLK1和CLK2是两个同步时钟信号，于是我们才会使用同步器对数据进行同步。

- FIFO：
  - 当两个时钟域的时钟信号不同步的时候，我们使用FIFO进行数据传输操作。
  - FIFO非常重要，之后遇到再整理......
  - ![](https://nickaljy-pictures.oss-cn-hangzhou.aliyuncs.com/img/photo_2022-10-06_18-14-10.jpg)



