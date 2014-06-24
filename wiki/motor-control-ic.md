
## Crazepony电机驱动选型故事
从一个几乎零基础的状态去选择电机驱动芯片，弯路是必须要走的，学费是必须要交的。曾以为书上学到的东西马上就能用，马上能转化为产品，后来发现真的是自己想多了。

最开始用的三极管作为电机驱动，采用很经典的共射电路“三极管工作在开关状态应该就行了吧？”画了用三极管驱动的PCB板，发现电机越转越慢，根本没劲。“也许是因为三极管扛不了大电流，好吧那我换个中功率管吧，集电极最大6A电流行了吧？”可以想象结果是不行的

首先了解下为什么三极管作为简单的电机驱动是不可取的方案：

* 三极管作为一个古老的半导体先驱，它是以一个放大器件的姿态而出现的，它在线性区域特性集中，饱和与截止都是两种极端的工作状态，而作为电机驱动的话，我们只能选择它的这两种极端工作模式
* 用三极管作为大电流负载的驱动管时，不得不考虑的是他自身的管压降对负载的影响，这是很严重的。自身耗散越来越大，电机和管子是串联关系，电池电压只有3.7V，电机就只能越转越慢了

在晶体管家族里面还有一种跟三极管特性互补的，所有特性都集中在开关状态的晶体管，场效应管，即MOSFET。通常的场效应管完全导通时，源漏极电阻都是mΩ级别的，即它自身的耗散非常小。用它做为驱动管再合适不过了。

最终选择了一个SOT23封装的,导通电压Vgs<4v的场管（SI2302）,结果表现出了很好的驱动性能。

## 无刷电机驱动控制

在《电机与桨叶》一文中，我们提到**大四轴基本上都是使用的无刷电机，无刷电机控制必须配合电调使用**。所以无刷电机的驱动控制就是电调。crazepony使用的时有刷空心杯电机，所以电调暂时和我们没有什么关系，但是我们了解一下，还是很有帮助的。

下面就说说电调。四轴飞行器有四个桨，两两相对呈十字交叉结构。在桨的转向上分正转和反转，这样可抵消单个桨叶旋转引起的自旋问题。每个桨的直径很小，四个桨转动时的离心力是分散的。不像直机的桨，只有一个能产生集中的离心力形成陀螺性质的惯性离心力，保持机身不容易很快的侧翻掉。所以通常用到的舵机控制信号更新频率很低。

四轴为了能够快速反应，以应对姿态变化引起的飘移，需要高反应速度的电调，常规PPM电调的更新速度只有50Hz左右，满足不了这种控制所需要的速度，且PPM电调MCU内置PID稳速控制，能对常规航模提供顺滑的转速变化特性，用在四轴上就不合适了，四轴需要的是快速反应的电机转速变化。用高速专用电调，IIC总线接口传送控制信号，可达到每秒几百上千次的电机转速变化，在四轴飞行时，姿态时刻能够保持稳定。即使受到外力突然冲击，依旧安然无恙。

## 扩展阅读

IT公司关于电机驱动和控制解决方IT公司关于电机驱动和控制解决方案案[IT公司关于电机驱动和控制解决方案](http://www.ti.com.cn/ww/motor_drive_and_control_solutions/index.htm)。