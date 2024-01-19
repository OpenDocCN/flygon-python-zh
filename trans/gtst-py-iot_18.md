# 使用 GPIO 作为输入

在上一章中，我们了解了 GPIO 如何用于输出。但是，正如其名称所示，GPIO 既可以用于输入也可以用于输出。在本章中，我们将看到如何使用这些引脚将数据输入到树莓派上。

本章我们将涵盖的主题有：

+   深入了解 GPIO

+   与 PIR 传感器的接口

+   与超声波接近传感器的接口

+   通过 I2C 进行接口

# 深入了解 GPIO

我相信你还记得上一章的这行代码：

```py
GPIO.setup(18,GPIO.OUT)
```

正如前面解释的，这基本上告诉我们在某个程序中 GPIO 引脚的行为。到现在为止，你一定已经猜到，通过改变这一行代码，我们可以改变引脚的行为，并将其从输出转换为输入。这就是你会这样做的方式：

```py
GPIO.setup(18,GPIO.IN)
```

一旦在程序中写入这行代码，微控制器将知道在程序运行时，引脚号`18`只用于输入目的。

要理解这实际上是如何工作的，让我们回到我们的硬件，看看它是如何完成的。首先，你需要将 LED 连接到任何一个引脚；在这个程序中，我们将使用引脚号`23`。其次，你需要在引脚号`24`上连接一个开关。你可以参考接下来的图表来进行连接：

![](img/d427ad85-89bc-4344-ad6d-be910c696a1a.png)

一旦连接好，你可以继续编写这个程序：

```py
import time import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BCM)
GPIO.setup(24,GPIO.IN)
GPIO.setup(23,GPIO.OUT)
while True:
  button_state = GPIO.input(24)
    if button_state == True:
      GPIO.output(23,GPIO.HIGH)
    else:
      GPIO.output(23,GPIO.LOW)
  time.sleep(0.5)
GPIO.cleanup()
```

一旦程序上传，当你按下按钮时，LED 将自行打开。

让我们了解到底发生了什么。`while True:`基本上是一个无限循环；一旦应用了这个循环，循环内运行的代码会一遍又一遍地重复，直到有什么东西打破它，而所谓的打破是指导致程序停止并退出的某种中断。现在，理想情况下，我们通过按下*Ctrl* + *C*来退出程序，每当有一个无限循环时。

```py
button_state = GPIO.input(24)  
```

在上面的一行中，程序理解它需要查找的地方；在这个程序中。在这一行中，我们告诉程序我们正在寻找 GPIO `24`，这是一个输入：

```py
if button_state == True:
   GPIO.output(23,GPIO.HIGH)  
```

如果按钮是高的，换句话说，当按钮被按下并且电流到达引脚号`24`时，那么 GPIO 引脚号`23`将被设置为高：

```py
  else:
   GPIO.output(23,GPIO.LOW)
```

如果引脚号`24`不为真，它将遵循这行代码，并保持引脚号`23`低，换句话说关闭。

所以，这就是你用于输入目的的第一个 GPIO 程序。

# 与 PIR 传感器的接口

到目前为止一切顺利！在这个单元中，我们将继续接口我们的第一个传感器，即被动红外传感器，通常称为 PIR 传感器。这个传感器是一个非常特殊的传感器，在自动化项目中非常常见。它的低能耗使其成为物联网项目的绝佳竞争者。所以让我们看看它是如何工作的。

你一定注意到了，当我们将金属加热到高温时，它慢慢变成深红色，当我们进一步加热时，它变得更亮，慢慢从红色变成黄色，如下图所示，显示了一个红热的钢片。现在，随着温度的升高，发射辐射的波长减小；这就是为什么随着温度的升高，颜色从红色变成黄色，因为黄色的波长比红色短。

![](img/92c35129-4393-4222-b0cf-ee8a9ae585d9.png)

但有趣的是，即使物体没有被加热到足够的温度，它们也会发射辐射；事实上，任何高于绝对零度温度的物体都会发射某种形式的辐射。有些我们能用肉眼看到，有些我们看不到。因此，在室温下，物体会发射红外辐射，其波长比可见光更长。因此，我们的眼睛看不到它。尽管如此，它仍然存在。

这个 PIR 传感器的作用是感知周围物体发出的红外光，每当物体移动时，它可以感知其模式的整体变化，并且基于此可以检测到其附近是否发生了任何运动。

我们假设当房间里有人时，会有一些固有的运动发生，因此这种传感器非常常用作占用传感器。现在，让我们连接这个传感器，看看我们如何使用它：

![](img/c64b669a-44f1-44ba-a145-09adac7bb2d8.png)

一旦您按照上图连接好了，就可以上传代码了：

```py
import time import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BCM)
GPIO.setup(23,GPIO.IN) 
GPIO.setup(24,GPIO.OUT)
while True:
 if GPIO.input(23) == 1: 
  GPIO.output(24,GPIO.HIGH)
 else: 
  GPIO.output(24,GPIO.LOW)

 time.sleep(1)
GPIO.cleanup()
```

现在，让我们看看发生了什么。逻辑非常简单。一旦 PIR 传感器检测到运动，它就会将输出引脚设置为高电平。我们所要做的就是监视该引脚，基本上就是这样。

逻辑与按键开关完全相似，它也会以类似的方式工作。因此，不需要太多解释。

# 接口超声波接近传感器

首先，基础知识。接近传感器是一种传感器，它可以感知到与其接近的物体。有许多传感器可以完成这项任务，以及许多技术可以让我们这样做。正如其名称所示，超声波接近传感器是基于超声波的工作原理。工作原理非常容易理解。超声波传感器发射一束超声波；这些波对人耳来说是听不见的，但它仍然是一种声波，它也像声波一样行为。

现在，我们知道声音会反射不同的表面并形成回声。当您在空房间说话时，您一定有过这种回声的经历。您可以听到自己的声音，但有轻微的延迟。这种延迟是由声音的特性引起的。声音是一种波，因此它有速度。声波有固定的传播速度。因此，为了覆盖特定的距离，它们需要一定的时间。通过计算这段时间，我们可以推导出声波在从表面反射之前走过的距离。

同样，在这种传感器中，我们向特定方向发射超声波，然后感知反射回来的回声。自然地，接收回声会有一定的延迟；这个延迟会与物体距离传感器的距离成正比，基于这个延迟，我们可以轻松计算出距离。

现在，要使用接近传感器，我们需要了解传感器的物理结构以正确接线。传感器上有四个引脚，它们是：

+   VCC（正极）

+   触发

+   回声

+   GND（地线）

显然我不需要解释 VCC 和地线的作用。因此，让我们直接转到触发。每当引脚高电平持续 10 微秒时，超声波传感器将向目标发送 8 个 40kHz 的声波周期。一旦触发周期完成，**ECHO**被设置为高电平。一旦接收到回声信号，**ECHO**引脚就会被设置回低电平。以下是一个图表，展示了它实际发生的过程：

![](img/6a520177-aaa6-416d-bf7a-a89a47338191.png)

这就是我们现在需要知道的全部。随后，随着我们的学习，我们会了解更多。现在，继续并让它运行起来，按照图表连接：

![](img/bb00f6f6-880c-4cb0-a37f-139d1a48dc2f.png)

连接完成后，需要运行以下代码：

```py
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
GPIO.setup(23,GPIO.OUT) 
GPIO.setup(24,GPIO.IN) 

while True:
     pulse_start = 0
     pulse_stop = 0
     duration = 0
     distance = 0

     GPIO.output(23,GPIO.LOW)
     time.sleep(0.1) 
     GPIO.output(23,GPIO.HIGH)
     time.sleep(0.000010)
     GPIO.output(23,GPIO.LOW)

     while GPIO.input(24)==0:
         pulse_start = time.time()

     while GPIO.input(24)==1:
         pulse_stop = time.time()

     duration = pulse_stop - pulse_start

     distance = duration*17150.0
     distance = round(distance,2)
     print ("distance" + str(distance)) 

     time.sleep(0.2)
}
```

现在，一旦您运行这个程序，屏幕上的输出将每 0.2 秒显示一次物体的距离。现在，您一定想知道这是如何传达所有这些读数的：

```py
GPIO.setup(23,GPIO.OUT) 
```

我们将分配引脚`23`在需要时给传感器的**TRIGGER**引脚提供脉冲：

```py
GPIO.setup(24,GPIO.IN)
```

我们将分配引脚`24`来接收逻辑以确认接收到回声信号：

```py
pulse_start = 0
 pulse_stop = 0
 duration = 0
 distance = 0
```

我们将使用上述作为变量，并且每次循环开始时，我们都会给它们赋值为`0`；这是为了清除在程序过程中存储的先前读数：

```py
GPIO.output(23,GPIO.HIGH)
  time.sleep(0.000010)
  GPIO.output(23,GPIO.LOW)
```

我们保持触发引脚编号`23`高 0.000010 秒，以便超声波传感器可以发送一脉冲超声波：

```py
  while GPIO.input(24)==0: 
 pulse_start = time.time()
```

这个 while 语句将一直记录`pulse_start`变量的时间，直到引脚编号`24`的时间为低电平。最终的时间读数将存储在`pulse_start`变量中，即记录脉冲发送的时间：

```py
while GPIO.input(24)==1:
 pulse_stop = time.time()
```

在这个循环中的`while`语句将开始记录引脚编号`24`高电平的时间，并将一直记录时间，直到引脚编号`24`保持高电平。时间的最终读数将存储在`pulse_stop`变量中，即记录脉冲接收的时间：

```py
 duration = pulse_stop - pulse_start
```

在这个声明中，我们正在计算脉冲从传感器到物体再反弹到传感器接收器所需的总时间：

```py
 distance = duration*17150.0
```

这是制造商提供的一个算术公式，用于将超声波传播所需的时间转换为厘米的实际距离。你可能会问我们是如何得到这个方程的？

让我简要介绍一下。通过初等物理，我们会记得这个简单的方程：*速度* = *距离* / *时间*。

现在你可能还记得声音的速度是 343 米每秒。现在 1 米有 100 厘米，因此要将这个速度转换为每秒厘米，我们必须将速度乘以 100，因此速度将是每秒 34,300 厘米。

现在我们知道方程的一个元素，即速度。所以让我们把速度的值放入方程中。现在方程看起来会是这样：*34,300* = *距离* / *时间*。

现在我们知道声音传播的距离是实际距离的两倍。为什么？因为声音首先从传感器传向物体。然后它从表面反射回来并到达传感器。因此，它实质上覆盖了两倍的距离。因此，为了适应这个方程，我们必须做出一个小改变：*34,300* / *2* = *距离* / *时间*

现在我们想从这个方程中得到距离，所以让我们把所有其他部分移到另一边。现在方程看起来会是这样：*17,150* * *时间* = *距离*

所以这里有距离的公式。

```py
distance = round(distance,2)
```

由于超声波传播的距离是实际距离的两倍（一次是朝物体前进，第二次是反弹到传感器），我们将其除以二得到实际距离：

```py
print 'Distance = ',distance
```

最后，我们将通过以下声明打印测得的距离。任何在引号内的内容`'...'`都将按原样写入。但是，`distance`没有引号，distance 是一个变量。因此，存储在距离中的变量将在屏幕上的最终输出中写入：

```py
 time.sleep(0.25)
```

代码将在这一行暂停 0.2 秒。如果没有这个暂停，那么值将以令人难以理解的速度出现，这对我们来说将很难阅读或理解。如果你在摆弄，我建议删除这个声明并运行代码看看会发生什么。

# 通过 I2C 进行接口

到目前为止，一切都很好。电子电路可能非常有趣，虽然它们看起来非常复杂，但我们经常发现工作非常简单。在前一节中，我们一次只接口一个传感器。我们可以继续接口多个传感器，但我们受到现有 GPIO 数量的限制。我们还看到一些传感器，如超声波传感器可能使用多个 GPIO 引脚进行工作。这进一步减少了我们可以与微控制器接口的传感器数量。一旦我们转向更复杂的电路，我们还会意识到布线可能会变得非常混乱，如果出现问题，找出问题所在将变得非常繁琐。

现在，我们在设计机器人系统时面临的一个更大的问题是时间的问题——系统中的所有工作都必顶同步。目前大多数系统都是顺序的，即一个单元的输出成为另一个单元的输入：

![](img/5a879310-c166-4e61-abad-0342052b4c5b.png)

现在，为了完成任务，当需要时，**处理单元 1**必须将输入传递给**处理单元 2**，**处理单元 3**也是如此。如果数据的时间不完美，那么**处理单元 2**要么会一直等待**处理单元 1**的输入，要么更糟糕的是，**处理单元 1**会在**处理单元 2**不需要数据的时候发送数据。在这种情况下，数据将丢失，过程将出现一些错误。

因此，为了解决这个问题，当时的计算机科学家发明了一种脉冲系统。时钟脉冲是一个非常简单的方波，具有 50%的占空比（回想一下脉冲宽度调制（PWM））。电路被设计为在时钟脉冲的上升沿或下降沿执行一次操作。由于这种同步，电路的每个部分都知道何时工作。时钟脉冲的样子如下：

![](img/f64ababd-856f-4586-98d6-47e74b222ce7.png)

现在，回到问题上，我们有两个问题：

+   机器人连接的设备/传感器存在物理限制

+   如何使传感器和互连电路的时间协调工作

为了解决这些问题，我们使用了一个非常常用的协议，称为 I2C，代表着互联集成电路。当我们需要在相同的 GPIO 上连接多个设备时，比如只有一组 GPIO 引脚可以连接多个传感器时，这个协议非常有用。这是由于为每个硬件分配了唯一的地址。该地址用于识别传感器，然后相应地与其通信。现在，要实现 I2C 协议，我们需要两条线路；这些线路如下：

+   数据

+   时钟

正如你可能已经猜到的那样，时钟线用于向连接的设备发送时钟脉冲，数据是数据流动的总线。

现在，整个 I2C 架构是基于主从配置工作的，其中主设备始终为从设备生成时钟信号，从设备必须不断寻找主设备发送的时钟脉冲和数据包。让我们看看是如何完成的。

如前所述，有两条线路：数据线称为串行数据（SDA），时钟线称为串行时钟（SCL）。从现在开始，我们将使用 SCL 和 SDA 这些术语：

![](img/4fc1c316-1172-4c9d-a435-6c062a673bf1.png)让我们看看图中显示的主要要点：

+   **起始条件**：为了开始通信，创建一个起始条件，表示通信即将发生。主设备通过在 SCL 之前保持 SDA 线低来表示这个条件。这表示所有从设备都准备好进行通信。

+   **地址帧**：一旦通信开始，主设备发送需要通信的设备的地址。这是一个 7 位地址。在每个时钟脉冲中，一个位被发送，因此需要七个时钟脉冲来发送 7 位地址。在这 7 位地址之后是读/写位。这表明设备是否在这个操作中想要写入，还是想要读取一些数据。因此，总地址帧是 8 位，需要八个时钟脉冲来发送。在这八个脉冲之后，在第九个时钟脉冲期间，主设备等待来自设备的确认。当 SDA 线被被寻址的从设备拉低时，从设备发送这个确认。通过这种策略，主设备知道它发送的地址已经被接收，并且从设备现在准备好进行通信。如果没有发送确认，那么由主设备决定接下来该做什么。

+   **数据帧**：一旦确认被发送，根据是读操作还是写操作，数据要么由主设备写入从设备，要么在读操作中，数据由从设备发送到主设备。这个数据帧的长度可以是任意的。

+   **停止帧**：一旦数据传输完成，主设备发出停止条件，表示通信必须停止。当 SDA 线在 SCL 线从低电平变为高电平后，此条件被执行。

这基本上就是 I2C 通信的工作原理。对于每个设备，我们有一个 7 位地址，因此我们可以在单个总线上连接多达 128 个设备。这是很多设备。几乎可以忽略物理限制用完的可能性。现在让我们继续看看如何通过这种协议连接传感器。通常，不需要为 I2C 进行核心编程，因为这很冗长和繁琐。这就是开源的魔力所在。全球有很多开发人员正在研究这些传感器，其中大多数人足够慷慨，制作了一个库并分享给大家以便编程。这些库可以在线获取，其中大多数库都处理了通信的复杂过程。

现在是我们接口第一个 I2C 设备的时候了，这是一个模拟到数字转换器。你一定会想为什么我们首先使用这个转换器。回想一下我们开始理解 GPIO 引脚的时候。这些神奇的引脚可以用作输入和输出；你可能还记得这些引脚可以是开或关状态——这些都是数字引脚，不仅在输出时，也在输入时。但是有大量的传感器是通过模拟通信工作的。由于树莓派的数字架构，直接接口这些传感器是困难的。因此，我们使用**模拟到数字转换器**（**ADC**），这个转换器将传感器的模拟值转换为树莓派可以理解的数字位。

我们将连接一个 LDR，电阻将根据光线的多少改变电阻值。因此，电压将取决于光线照射在 LDR 上的多少。

现在让我们看看如何实际操作。拿起你的树莓派，让我们开始吧。首先，我们需要在树莓派上启用 I2C；按照这里列出的步骤进行操作：

1.  打开终端（*Ctrl* + *Shift* + *T*）

1.  输入`sudo raspi-config`

1.  选择接口选项：

![](img/c42f873e-fc46-4598-b399-6153ec9543d3.png)

1.  然后转到高级选项：

![](img/72d2ad53-f4e4-44a9-8efc-e866f072c910.png)

1.  然后选择 I2C 以启用它。然后选择是：

![](img/674dfc12-0f37-469c-958a-06bd15727f78.png)

现在安装`adafruit`库以接口 ADC1115：

1.  打开终端并复制以下命令：

```py
sudo apt-get install build-essential python-dev python-smbus python-pip 
```

这个命令将库和依赖项下载到树莓派上

1.  现在输入以下内容：

```py
sudo pip install adafruit-ads1x15
```

这个命令将库和依赖项安装到树莓派上。

现在软件已经设置好了，让我们准备好硬件。按照下图将树莓派连接到 ADS1115：

![](img/4444a5ef-efce-4a02-b51d-7d9c20970323.png)

准备好后，继续在 Pi 上上传这段代码：

```py
import time
import Adafruit_ADS1x15
import RPi.GPIO as GPIO
LED =14

GPIO.setmode(GPIO.BCM)
GPIO.setup(LED,GPIO.OUT)

adc = Adafruit_ADS1x15.ADS1115()
GAIN = 1
channel=0
adc.start_adc(channel, gain=GAIN)

while True:
    value = adc.get_last_result()
    print(str(value))
    time.sleep(0.1)
    if value >= 100:
        GPIO.output(LED,1)
    else :
        GPIO.output(LED,0)

adc.stop_adc()
```

请注意，有时这段代码可能不起作用，如果是这样，请尝试调整阈值的值：

```py
if value >= 100:
```

你可能已经注意到，每当 LDR 面向光源时，LED 也会亮起，而当它远离光线时，LED 会熄灭。

现在你已经接口了一个 I2C 设备。让我们了解这段代码实际上是如何工作的：

```py
import Adafruit_ADS1x15
```

上一行代码导入了`Adafruit_ADS1x15`库，以便我们在程序中使用它的所有函数。

```py
adc = Adafruit_ADS1x15.ADS1115()
```

上一行代码创建了库`Adafruit_ADS1x115`的实例。`.ADS1115()`是创建实例`adc`的函数。明白了吗？让我用英语解释一下。

现在，我们可以简单地写`adc`而不是一直写`Adafruit_ADS1x15`，来调用库函数。此外，你可以使用任何单词代替`adc`；它可以是你猫的名字或你邻居的名字，它仍然可以工作：

```py
GAIN = 1
```

这是传感将进行的值。`1`表示传感将在整个范围内进行。对于我们的 ADC 来说，范围是从 0V 到+/-4.096V 的电压范围。现在改变增益会导致传感范围的改变。也就是说，如果我们将增益值更改为`2`，那么传感范围将是原始范围的一半，即 0 到+/-2.048 伏。

现在你可能会问电压范围是多少，为什么我们要改变增益？

原因很简单。有不同类型的模拟传感器。它们的输出电压范围各不相同。有些传感器的输出范围是 0.5 伏到 4 伏，其他的可以是 0.1 伏到 0.98 伏。现在，如果我们将增益设置为`1`，那么所有这些传感器都可以轻松接口。因为它们都在 0 到 4.098 伏的感应范围内。然而，由于它是一个 16 位 ADC，因此 ADC 可以提供的离散值的总数将在 2¹⁶或 65,536 个读数之间。因此，在增益为`1`时，ADC 可以检测到的最小电压变化为：*4.096* / *65536* = *0.000062*。

但是，如果增益增加到`4`，那么传感范围将减少到仅为`0`到+/-1.0245。因此，这将能够处理 0.1 伏到 0.98 伏之间的输出范围。但现在让我们看看它可以检测到的最小电压变化：*1.0245* / *65536* = *0.00001563*。

现在你可以看到可以检测到的最小电压非常低。这对于与传感器的兼容性是一件好事。

现在，你可以决定你想要什么增益值。LDR 在 5V 上工作，因此最好使用整个增益读数为`1`：

```py
channel=0
```

当你仔细观察 ADC 硬件时，你会注意到有各种引脚，包括**A0**、**A1**、**A2**和**A4**。这是一个四通道 ADC——它可以将四个模拟输入转换为数字数据。由于我们只使用一个数据流，我们将让 Pi 知道它连接在哪个引脚上。通过下面的代码，我们告诉 Pi 开始转换数据的过程：

```py
adc.start_adc(channel, gain=GAIN)
```

在下一行中，我们指示 ADC 停止转换，代码到此结束。

```py
adc.stop_adc()
```

# 摘要

本章主要讲述了如何将传感器与 GPIO 进行接口，以便传感器可以检索数据。