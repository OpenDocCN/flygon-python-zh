# 第二十一章：蓝牙控制的机器人车

我们已经走了很长的路；现在是时候继续前进，做出更好的东西。世界正在为自动驾驶汽车的诞生而疯狂，这将成为新的常态。这些车辆中有很多技术。多个传感器、GPS 和遥测都实时计算，以确保车辆在正确的路线上安全行驶，因此制作一个机器人车辆被证明是学习机器人技术和未来技术的理想方式。在这本书中，我们将尝试制造不仅与现有技术一样好，而且在某些方面甚至更好的技术。所以，让我们继续，一步一步地制作这辆自动驾驶车辆。

本章将涵盖以下主题：

+   车辆的基础知识

+   准备车辆

+   通过蓝牙控制车辆

# 车辆的基础知识

你一定在想：我们可能还能从这辆车上学到什么呢？这可能是真的，但在开始这一章之前，我们必须确保理解其中的一些内容。所以，让我们开始吧。

首先是我们将使用的底盘：这是一个四轮驱动底盘，所有四个车轮都由专用电机独立控制。因此，我们可以根据需要改变每个车轮的速度。我们选择了四轮驱动传动系统，因为它不容易在地毯和不平整的表面上被卡住。如果你愿意，你也可以选择两轮驱动传动系统，因为这不会有太大的区别。

现在，一旦你组装好底盘，你可能会发现它没有转向机构。这是否意味着车只能直行？显然不是。在制作小型车辆时，有许多方法可以改变车辆的方向。最好的方法被称为差速转向。

在传统汽车中，有一个发动机，这个发动机驱动车轮；因此原则上所有车轮以相同的速度转动。现在当我们直行时这很好用，但每当车要转弯时就会出现一个新问题。参考以下图表：

![](img/64c30dee-9086-4d1c-88ea-4f64dd2e5b79.png)

你会看到内侧的车轮直径较小，外侧的车轮直径较大。你可能还记得小学的一个事实：直径越大，周长越大，反之亦然。因此，内侧的车轮在同一时间内将行驶较短的距离，或者简单地说，内侧的车轮会转得更慢，外侧的车轮会转得更快。

这个问题导致了汽车差速器的发现，它是汽车轴的中心有一个圆形的凸起。它的作用是根据转弯半径改变车轮的旋转速度。天才，不是吗？现在，你可能会想：这都没错，但你为什么要告诉我这些？因为我们将做相反的操作来转动机器人。如果我们改变转向圈内外边缘电机的速度，那么车辆将试图向内转向，同样，如果我们对另一端这样做，它将试图向另一个方向转向。在制作轮式机器人时，这种策略并不新鲜。转向机构很复杂，在小型机器人上实现它们只是一个挑战。因此，这是一个更简单和容易的方法来转动你的车辆。

这种方式不仅简单而且非常高效，需要的零部件也很少。车辆的转弯半径也更小。事实上，如果我们以相同速度将车轮的相对侧向相反方向旋转，那么车辆将完全围绕自己的轴旋转，使转弯半径完全为零。这种配置称为履带转向驱动。对于室内使用的轮式机器人来说，这是一个杀手功能。

要了解更多，请阅读这里：[`groups.csail.mit.edu/drl/courses/cs54-2001s/skidsteer.html`](https://groups.csail.mit.edu/drl/courses/cs54-2001s/skidsteer.html)

# 准备车辆

现在是时候继续让机器人车辆成为现实了。所以让我们打开车辆底盘并将每个零件螺丝拧在一起。组装手册通常随套件一起提供，所以你很快就能完成它。

完成组装套件后，继续将每个电机的电线分开。这将是使车辆准备就绪的非常重要的部分。因此，一旦车辆上所有的电线都出来了，拿一个电池，给每个车轮供电。注意连接的极性，车轮是向前旋转的。你需要做的就是拿一个永久性的记号笔或者指甲油，标记电线，当电机向前旋转时，连接到正极的电线。由于所有这些电机完全依赖于极性来确定方向，这一步是关键，以确保无论何时给它们供电，它们总是以相同的方向旋转。相信我，这将为你节省很多麻烦。

现在，一旦这一切都完成了，按照以下图示将电线连接到电机驱动器（红色标记的电线是你之前标记的电线）：

![](img/d6fcc277-16be-41d1-8163-0af48b9a70b0.png)

完美！现在一切似乎都准备好了，除了电机驱动器与电源和树莓派的连接。所以让我们看看我们将如何做到： 

![](img/8ba50ebd-202a-45f1-9506-8f71f3f3430a.png)

好了！是时候进行真正的交易了！所以我们要确保的第一件事是所有的连接都按照我们计划的方式工作。为此，我们将从一个简单的代码开始，它将简单地打开所有电机并向前旋转。所以这是代码：

```py
import RPi.GPIO as GPIO
import time GPIO.setmode(GPIO.BCM) Motor1a = 20 Motor1b = 21 Motor2a = 2 Motor2b = 3  GPIO.setup(Motor1a,GPIO.OUT) GPIO.setup(Motor1b,GPIO.OUT) GPIO.setup(Motor2a,GPIO.OUT) GPIO.setup(Motor2b,GPIO.OUT) GPIO.output(Motor1a,1) GPIO.output(Motor1b,0) GPIO.output(Motor2a,1) GPIO.output(Motor2b,0)
time.sleep(10)
GPIO.cleanup() 
```

程序不可能比这更简单了；我们在这里所做的就是向电机驱动器发出命令，让电机单向旋转。可能会有一组电机会以相反方向旋转的情况，这种情况下你应该改变电机驱动器上的连接极性。这应该解决问题。有些人可能会认为我们也可以对代码进行更改来解决这个问题，但根据我的经验，从那里开始就会变得复杂，并且如果你选择其他路径，会给你带来麻烦。

好了，一切都准备就绪，一切都运行良好。继续尝试一些其他输出排列组合，看看车子会发生什么。别担心，无论你做什么，除非它从屋顶上跑下来，否则你都不会损坏车子！

# 通过蓝牙控制车辆

玩了一些尝试这些组合的乐趣吗？现在是时候我们继续前进，看看还有什么其他可能性了。我们都玩过遥控车，我相信每个人都会对那些敏捷的小玩具感到开心。我们将做类似的事情，但以一种更复杂的方式。

我们都知道蓝牙：这是与附近设备通信的最佳方式之一。蓝牙通信是一种中等数据速率、低功耗的通信方法。这在移动设备中几乎无处不在，因此是一个理想的开始方式。在本章中，我们将通过蓝牙使用手机来控制车辆。现在让我们看看我们如何做到这一点。

我们想要做的第一件事是将智能手机与机器人车配对，为此我们需要在树莓派上打开终端并执行以下步骤：

1.  在命令行中输入`~ $ bluetoothctl`；这是一个蓝牙代理，允许两个蓝牙设备进行通信。没有蓝牙代理，这两个设备首先就无法进行通信。

1.  `[Bluetooth] # power on`命令简单地启动了树莓上的蓝牙。

1.  `[Bluetooth] # agent on`命令启动代理，然后可以为我们启动连接。

1.  `[Bluetooth] # discoverable on`命令使树莓派的蓝牙可发现。蓝牙可能已经打开，但我们必须使其可发现，以确保其他设备可以找到它并连接到它。

1.  `[Bluetooth] # pairable on`命令使设备可配对。如果蓝牙已打开，这并不意味着您的设备将能够连接，因此我们需要使其可配对，这个命令正是这样做的。

1.  `[Bluetooth] # scan on`命令开始扫描附近的蓝牙设备。这个命令的输出将是一些 MAC 地址以及蓝牙名称。MAC 地址是设备的物理地址；这是一个唯一的地址，因此它永远不会对两个设备相同。

1.  `[Bluetooth] # pair 94:65:2D:94:9B:D3`命令帮助您与您想要的设备配对。您只需输入带有 MAC 地址的命令。

只是为了明确，这是您的屏幕应该看起来的样子：

![](img/4f539ab7-328e-4d73-b82f-cffd3bcfc089.png)

完成了这个过程后，您应该能够将树莓派连接到您的移动设备。现在您已经连接，是时候继续编写代码了，通过这些代码，我们将能够仅使用移动设备来控制蓝牙汽车。所以这是代码。继续，看一看，然后我们将进行解释：

```py
import bluetooth
import time
import RPi.GPIO as GPIO
Motor1a = 20
Motor1b = 21
Motor2a = 2
Motor2b = 3
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(Motor1a,GPIO.OUT)
GPIO.setup(Motor1b,GPIO.OUT)
GPIO.setup(Motor2a,GPIO.OUT)
GPIO.setup(Motor2b,GPIO.OUT)
server_socket=bluetooth.BluetoothSocket( bluetooth.RFCOMM )
port = 1
server_socket.bind(("",port))
server_socket.listen(1)
client_socket,address = server_socket.accept()
print ("Accepted connection from "+str(address))
def stop_car():
  GPIO.output(Motor1a,0)
  GPIO.output(Motor1b,0)
  GPIO.output(Motor2a,0)
  GPIO.output(Motor2b,0)

while True:
  data = client_socket.recv(1024)
  if (data == "B" or data== "b"):
    GPIO.output(Motor1a,1)
    GPIO.output(Motor1b,0)
    GPIO.output(Motor2a,1)
    GPIO.output(Motor2b,0)
    time.sleep(1)
    stop_car()

  if (data == "F" or data == "f"):
    GPIO.output(Motor1a,0)
    GPIO.output(Motor1b,1)
    GPIO.output(Motor2a,0)
    GPIO.output(Motor2b,1)
    time.sleep(1)
    stop_car()

  if (data == "R" or data == "r"):
    GPIO.output(Motor1a,0)
    GPIO.output(Motor1b,1)
    GPIO.output(Motor2a,1)
    GPIO.output(Motor2b,0)
    time.sleep(1)
    stop_car()

  if (data == "L" or data == "l"):
    GPIO.output(Motor1a,1)
    GPIO.output(Motor1b,0)
    GPIO.output(Motor2a,0)
    GPIO.output(Motor2b,1)
    time.sleep(1)
    stop_car()

  if (data == "Q" or data =="q"):
    stop_car()

  if (data =='Z' or data == "z"):
    client_socket.close()
    server_socket.close() 
```

现在让我们看看这段代码实际在做什么：

```py
import bluetooth
```

在这个程序中，我们将使用蓝牙的一些通用功能，因此我们调用`bluetooth`库，以便能够调用这些方法：

```py
server_socket=bluetooth.BluetoothSocket( bluetooth.RFCOMM )
```

现在，每当我们连接两个蓝牙设备时，我们有各种通信方法；其中最简单的是无线电频率通信，这里称为`RFCOMM`。现在，在这一行中，我们使用`bluetooth`库的`BluetoothSocket`方法来定义我们在程序中使用的通信协议，现在你已经知道是`RFCOMM`。我们进一步将这些数据存储在一个名为`server_socket`的变量中，这样我们就不必一遍又一遍地重复这个步骤。而是，每当我们需要这些数据时，它将已经存储在名为`server_socket`的变量中：

```py
port = 1
```

现在，蓝牙有多个端口；这是一个非常有用的概念，通过一个单一的蓝牙连接，我们可以将各种数据流传输到各种设备和程序。这避免了数据的冲突，并确保数据安全地传输到确切的接收者。我们现在使用的程序非常简单，我们不需要多个端口进行数据通信。因此，我们可以使用任何`1`到`60`个可用端口进行通信。在程序的这一部分，您可以写任何端口，您的程序将正常运行：

```py
server_socket.bind(("",port))
```

现在，每当我们连接两个设备时，我们需要确保它们在整个通信过程中保持连接。因此，在这里我们写下这个命令：`server_socket.bind`。这将确保您的蓝牙连接在整个通信过程中保持连接。

正如您所看到的，参数中的第一个参数是空的。在这里，我们通常写下它必须绑定的 MAC 地址。然而，由于我们将其设置为空，它将自动绑定到我们已经配对的 MAC 地址。我们的第二个参数是它必须连接的端口。正如我们所知，`port`变量的值被设置为`1`。因此，它将自动连接到端口号`1`：

```py
server_socket.listen(1)
```

这是一条非常有趣的线。正如我们所知，我们可能不是唯一一个尝试连接到树莓的蓝牙设备的人，因此当树莓接收到另一个连接请求时，树莓应该怎么做呢？

在这一行中，我们只是在定义：我们正在调用一个名为`listen(1)`的方法。在这个函数中，我们已经将参数的值定义为`1`。这意味着它只会连接到一个设备。任何其他尝试连接的设备都无法通过。如果我们将这个参数改为`2`，那么它将连接到两个设备，但它会留在队列中，因此被称为**队列连接**：

```py
client_socket,address = server_socket.accept()
```

现在大部分连接的事情都已经完成，我们还需要知道我们是否连接到了正确的地址。`server_socket.accept()`方法的作用是返回套接字号和它正在服务的地址。因此，我们将其存储在两个名为`client_socket`和`address`的变量中。然而，正如我们所知，套接字将仅保持为`1`，因此我们将不会再使用它：

```py
print ("Accepted connection from "+str(address))
```

在这一行中，我们只是告诉用户连接已成功建立，通过使用`str(address)`函数，我们打印连接到的地址的值。这样我们可以确保连接已经建立到了正确的设备。

```py
data = client_socket.recv(1024)
```

在这一行中，我们正在从客户端接收数据；同时，我们正在定义数据的长度。因此，在方法`client_socket.recv(1024)`中，我们在参数中传递了一个参数`1024`，这基本上表示数据包的最大长度为`1024`字节。一旦接收到数据，它就会传递给变量`data`供进一步使用。

在此之后，程序的其余部分非常简单。我们只需要比较移动设备接收到的值，并让汽车做我们想做的事情。在这里，我们让汽车向四个方向行驶，即前进、后退、右转和左转。您也可以根据自己的需求添加特定条件：

```py
 client_socket.close()
```

在这一行中，我们正在关闭客户端套接字的连接，以便断开客户端并终止数据传输：

```py
 server_socket.close()
```

在前一行中，我们正在关闭服务器套接字的连接，以便断开服务器连接。

# 总结

本章教会了我们如何使用蓝牙接口通过数据抓取和共享来自动化和控制汽车。接下来，我们将开发我们迄今为止所学到的内容，以便为避障和路径规划接口红外传感器。
