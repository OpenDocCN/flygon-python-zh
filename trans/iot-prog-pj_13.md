# 介绍树莓派机器人车

我想向您介绍 T.A.R.A.S，这辆机器人车。T.A.R.A.S 实际上是一个回文缩略词；我从一个帮助我起步的商业导师那里得到了这个名字。在绞尽脑汁之后，我终于想出了如何将我的朋友 Taras 变成 T.A.R.A.S，这个令人惊叹的树莓派自动安全代理。从名字上您可能能够猜到，T.A.R.A.S 将为我们监视事物并充当自动安全警卫。

T.A.R.A.S 将使用树莓派作为大脑和电机驱动板，以控制摄像机云台和车轮的运动。T.A.R.A.S 还将具有感应输入以及 LED 和蜂鸣器输出。T.A.R.A.S 将是我们在本书中所学技能的集合。

我们将在本章中组装 T.A.R.A.S 并编写控制代码。

本章将涵盖以下主题：

+   机器人车的零件

+   构建机器人车

# 机器人车的零件

我设计 T.A.R.A.S 尽可能简单地组装。T.A.R.A.S 由激光切割的硬纸板底盘、3D 打印的车轮和摄像头支架部件组成（也有使用激光切割车轮支架的选项）。为了您能够组装 T.A.R.A.S，我提供了底盘的 SVG 文件和 3D 打印部件的 STL 文件。所有其他零件可以在线购买。以下是 T.A.R.A.S 组装前的照片：

![](img/1a8a2578-d9ac-4388-9038-cfa92762062f.png)

1.  伺服摄像头支架（已组装）—在[www.aliexpress.com](http://www.aliexpress.com)搜索防抖动摄像头支架

1.  3D 打印支架（摄像头支架）

1.  车轮用直流电机（带有电机线和延长线）—在[www.aliexpress.com](http://www.aliexpress.com)搜索智能车机器人塑料轮胎轮

1.  车轮支架（3D 打印）

1.  LED 灯

1.  LED 灯座—在[www.aliexpress.com](http://www.aliexpress.com)搜索灯 LED 灯座黑色夹子

1.  摄像头支架支撑（激光切割）

1.  有源蜂鸣器—在[www.aliexpress.com](http://www.aliexpress.com)搜索 5V 有源蜂鸣器

1.  距离传感器（HC-SR04）—在[www.aliexpress.com](http://www.aliexpress.com)搜索 HC-SR04

1.  替代车轮支架（激光切割）

1.  树莓派摄像头（长焦镜头版本，不带电缆）—在[www.aliexpress.com](http://www.aliexpress.com)搜索

1.  电机驱动板（激光切割）

1.  车轮—在[www.aliexpress.com](http://www.aliexpress.com)搜索智能车机器人塑料轮胎轮

1.  机器人车底盘（激光切割）

1.  电机驱动板—在[www.aliexpress.com](http://www.aliexpress.com)搜索 L298N 电机驱动板模块

1.  DC 插孔（带有连接的电线）—[www.aliexpress.com](http://www.aliexpress.com)

1.  Adafruit 16 通道 PWM/舵机 HAT—[`www.adafruit.com/product/2327`](https://www.adafruit.com/product/2327)

1.  树莓派

1.  40 针单排公针排连接器条（未显示）—[www.aliexpress.com](http://www.aliexpress.com)

1.  各种松散的电线和面包板跳线（未显示）—购买许多不同的电线和面包板跳线是个好主意；您可以在[www.aliexpress.com](http://www.aliexpress.com)搜索面包板跳线

1.  热缩管（未显示）

1.  带 DC 插头的 7.4V 可充电电池（未显示）—在[www.aliexpress.com](http://www.aliexpress.com)搜索 7.4V 18650 锂离子可充电电池组（确保选择与 16 号 DC 插孔匹配的电池）

1.  与零件 22 相比，您可以使用 AA 号电池存储盒来替代零件 16 和 22—[www.aliexpress.com](http://www.aliexpress.com)

1.  迷你面包板（未显示）—在[www.aliexpress.com](http://www.aliexpress.com)搜索 SYB-170 迷你无焊点原型实验测试面包板

1.  各种支架（未显示）-它应该能够具有至少 40 毫米的支架高度；最好尽可能多地使用支架，因为它们似乎总是派上用场，您可以在[www.aliexpress.com](http://www.aliexpress.com)上搜索电子支架

1.  330 和 470 欧姆电阻器（未显示）-购买许多电阻器是个好主意；在[www.aliexpress.com](http://www.aliexpress.com)上搜索电阻器包

1.  便携式 USB 电源包（未显示）-这种类型用于在外出时给手机充电；我们将使用此电源包为树莓派供电

# 组装机器人汽车

以下是构建 T.A.R.A.S，我们的机器人汽车的步骤。您的 T.A.R.A.S 版本可能与本书中使用的版本相似，也可以根据需要进行修改。首先，我使用了带有较长镜头的树莓派相机模块（夜视模型带有较长的镜头）。我还使用 Adafruit 16 通道 PWM /伺服 HAT 来驱动相机支架的伺服。您可以选择使用另一个板，或者放弃伺服，将相机安装在固定位置。

我最喜欢的机器人缩写词之一是来自 1980 年迪士尼电影《黑洞》的 Vincent。 Vincent，或更准确地说，V.I.N.CENT，代表着必要的集中信息。如果您知道这部电影，您将知道 V.I.N.CENT 非常聪明和非常有礼貌。 V.I.N.CENT 也有点自以为是，有时可能有点烦人。

我提供了两种不同的安装轮毂电机的方法：使用 3D 打印的轮毂支架或使用激光切割的轮毂支架。我更喜欢 3D 打印的支架，因为它可以使螺丝嵌入，从而在底盘和轮毂之间提供更多空间。

如果您自己 3D 打印轮毂支架和相机支架，可以使用任何您喜欢的固体丝材类型。就我个人而言，我使用 PETG，因为我喜欢它的弯曲性而不易断裂。 PLA 也可以。请确保将轮毂支架侧向打印，以便它们打印宽而不是高。这将导致打印可能在孔周围有点凌乱（至少对于 PETG 来说），但它将是一个更坚固的零件。我设法在 30 分钟内打印了一个轮毂支架，相机支架大约 90 分钟。

组装机器人汽车应该花费您一下午的时间。

# 第 1 步 - 用于树莓派的 Adafruit 16 通道 PWM /伺服 HAT

如果您还没有听说过，纽约市有一家名为 Adafruit 的令人惊叹的公司，为全球的电子爱好者提供服务。 Adafruit 为树莓派创建了许多**HATs**（**Hardware Added on Top**），包括我们将用于机器人的 Adafruit 16 通道 PWM /伺服 HAT。

使用此 HAT，用于控制伺服的重复时间脉冲从树莓派卸载到 HAT 上。使用此 HAT，您可以控制多达 16 个伺服。

以下是 HAT 和随附的引脚的照片：

！[](assets/ef44e937-759e-4d95-b942-8d35b5e04834.png)

为了我们的目的，我们需要在板上焊接引脚：

1.  由于我们只使用了两个伺服，因此需要将两个**3 引脚伺服引脚**焊接到板上。

1.  焊接**2 X 20 引脚引脚**。在焊接时，固定板和引脚的一个好方法是使用一些橡皮泥！（确保在焊接时不要让热焊铁太靠近橡皮泥！）：

！[](assets/274703ea-3d65-4fac-a024-5e0fede76158.png)

1.  由于我们将使用来自电机板的电线来为伺服板供电，因此需要将电源引脚焊接到板上。

1.  我们需要从树莓派访问 GPIO 引脚，因此必须添加另一排引脚。从 40 针引脚排连接器中断开 25 根引脚。将引脚焊接到板上：

！[](assets/f578020c-805e-435f-94a1-393d2b501af3.png)

# 第 2 步 - 接线电机

我们需要将电机接线，以便两个电机始终同时且在同一方向旋转：

1.  切割八根相等长度的电线，并剥离所有电线的两端的一些绝缘：

![](img/11481a24-5dca-4873-936d-de7e12c8a928.png)

1.  将每个电机的端子焊接在一起：

![](img/c3081fa8-76c7-434b-97ce-f1b490d32dd6.png)

1.  在端子上应用热缩管以绝缘：

![](img/50e0c531-01fd-4a60-91e7-0e3c9d9fac51.png)

1.  将每个电机的电线分组并连接起来，使得一个电机顶部的电线连接到另一个电机底部的电线（请参考照片以了解清晰情况）：

![](img/249bbb07-2500-4f65-ad99-5c26bab1f08e.png)

1.  为了增加强度和保护，您可以使用热缩管将电线固定在一起（在上一张照片中，使用了黄色热缩管）。

1.  可以在末端添加延长线（我选择为我的组装添加了延长线，因为电线长度有点短）。稍后添加到末端的蓝色标签胶带将有助于将电机连接到电机驱动器板：

![](img/56bc16f2-2da7-41e4-a6ba-2a66d18f54a3.png)

# 第 3 步 - 组装伺服摄像机支架

有了我们的伺服摄像机支架，T.A.R.A.S 有能力左右摇头和上下运动。这对我们在第十四章中的项目非常有用，即*使用 Python 控制机器人车*。当您将伺服摄像机支架的零件倒在桌子上时，可能会感到有点令人生畏，不知道如何将其组装成有用的东西。

以下是伺服摄像机支架零件的照片。与其试图命名零件，我会把字母放下来，并参考这些字母进行组装：

![](img/c0b0a161-8760-4f1a-a995-b24a37ca20bd.png)

要组装伺服摄像机支架，请按以下步骤进行：

1.  将零件**E**放入零件**A**中，使得零件**E**的突出圆柱朝上：

![](img/ee0163cd-7696-4b42-8b25-3032cd731a5e.png)

1.  翻转并使用包装中最小的螺丝将零件**E**螺丝固定到零件**A**上：

![](img/05d60291-48b3-4962-bb76-d95629bfa5cb.png)

1.  使用小螺丝将伺服螺丝固定在零件**D**上（请参见下一张照片）：

![](img/56361793-a726-4c89-9ea4-4990fe63dde9.png)

1.  将零件**B**放在伺服上，并将零件**F**插入为其制作的凹槽中。将零件**F**螺丝固定在位。伺服应能够在连接到零件**B**和**F**的同时自由上下移动：

![](img/fc2f337b-1d27-4b11-a7da-0756e63cbb1a.png)

1.  翻转组装好的零件并将另一个伺服插入零件**B**中：

![](img/596a4f93-7534-4aef-a120-1ed776b46e64.png)

1.  将零件**C**放在伺服上。您可能需要稍微弯曲零件**D**以使零件**C**适合：

![](img/40c91b14-9ee6-447d-aba8-b8aa5d5296c2.png)

1.  翻转组装好的零件并将零件**B**和**C**螺丝固定在一起：

![](img/43f9b60d-e642-4861-86a8-cf20f0037b7a.png)

1.  将组装好的零件插入零件**E**中：

![](img/745d14a3-4613-4369-9438-a361241f02f5.png)

1.  将零件**A**螺丝固定在组装好的零件底部：

![](img/23bb8986-9f34-4c57-8251-3bb3dfc15b39.png)

# 第 4 步 - 附加头部

让我们面对现实吧。除非有某种面孔（向 R2D2 道歉），否则机器人就不是机器人。在这一步中，我们将连接零件以构建 T.A.R.A.S 的头部和面部。

根据 Rethink Robotics 创始人 Rodney Brooks 的说法，机器人并不是为了让它们看起来友好才有脸。机器人的脸被用作人类的视觉线索。例如，如果机器人朝某个方向移动头部，我们可以安全地假设机器人正在分析那个方向的东西。当我们移动 T.A.R.A.S 的头部时，我们向周围的人发出信号，告诉他们 T.A.R.A.S 正在朝那个方向看。

以下是完成头部所需的零件的照片：

![](img/15c557ce-1c73-4395-813b-8619a0770596.png)

现在我们将组装 T.A.R.A.S 的头部。以下是零件清单：

+   **A**：树莓派摄像头模块

+   **B**：摄像机支架支撑

+   **C**：带伺服的组装摄像机支架

+   **D**：3D 打印支架

+   **E**：距离传感器

+   **F**：螺丝

要组装头部，请按以下步骤进行：

1.  在树莓派摄像头模块和距离传感器上贴上双面泡沫胶带的小块：

![](img/ba566c64-a12f-4435-9c5b-f978f980068c.png)

1.  将树莓派摄像头模块和距离传感器粘贴到 3D 打印支架的适当位置（请参考以下照片以获得澄清）：

![](img/2db37d5e-c365-4453-9a65-748ab09d07b3.png)

1.  将组件滑入已组装的摄像头支架上并将其固定在位（请参考以下照片以获得澄清）：

![](img/ac938a4b-5a96-4efd-94e8-fce25cf85af5.png)

1.  在距离传感器的引脚上加上母对母跳线。在这里，我使用了一个四针连接器连接到 4 个单独的引脚。您也可以使用单独的跳线：

![](img/5a76c2d7-ff97-40ec-8bcd-17ec77c3d6f1.png)

1.  将组装好的部件转过来，贴上牙齿的贴纸：

![](img/011f8ceb-3127-447a-b352-03e0498f3c54.png)

# 步骤 5 - 组装直流电机板

直流电机板位于 T.A.R.A.S 的后部，安装有驱动轮的直流电机驱动器。直流插孔和尾灯 LED 也安装在直流电机板上。我们将从创建尾灯 LED 开始这一步。

以下照片显示了制作尾灯 LED 所需的零件：

![](img/3bf3391b-fd10-451b-9a8e-f87b2e4c5c59.png)

以下是零件清单：

+   **A**：红色跳线（一端必须是女头）

+   **B**：棕色跳线（一端必须是女头）

+   **C**：红色 LED

+   **D**：绿色 LED

+   **E**：330 欧姆电阻

+   **F**：热缩管

以下是创建 LED 尾灯的步骤：

1.  将 330 欧姆电阻焊接到 LED 的阳极（长腿）。

1.  在连接处加上热缩管以提供强度和绝缘。

1.  剥开一根红色跳线（确保另一端是女头），并将其焊接到电阻的末端。这是组件的正端：

![](img/90fb2cdb-e6f4-45c9-85de-732f8938f539.png)

1.  在整个电阻上加上热缩管：

![](img/4659ec3e-a64c-4481-be31-14f1d03bf841.png)

1.  将棕色线焊接到阴极并加上热缩管（在这张照片中，我们展示了一个带有延长棕色线的红色 LED）。这是组件的负端：

![](img/cc7440c0-7751-4ed8-8035-83d94a75cee4.png)

1.  现在我们已经完成了两个尾灯的组装，让我们把直流电机板组装起来。以下是我们需要组装直流电机板的零件的照片：

![](img/9277defa-8ea8-4dd4-87a1-2de3937a3fd3.png)

以下是零件清单：

+   **A**：红色尾灯

+   **B**：绿色尾灯

+   **C**：短电源线

+   **D**：电机驱动板

+   **E**：直流电机板（激光切割）

+   **F**：LED 支架

+   **G**：直流插孔

+   **H**：40mm 支架

+   八颗 M3X10 螺栓（未显示）

+   四颗 M3 螺母（未显示）

+   拉链扎带（未显示）

让我们开始组装它：

1.  用四颗 10mm M3 螺栓将 40mm 支架（**H**）固定到**E**上。请参考以下照片以获得正确的方向：

![](img/239e3c1c-63b2-43e1-acba-5829f74284a1.png)

1.  用四颗 10mm M3 螺栓和螺母将电机驱动板（**D**）固定到**E**上。请参考以下照片以获得正确的方向：

![](img/60030ea6-e7e6-4eca-af03-6571d7c8beba.png)

这是侧视图：

![](img/973a1d78-3237-4bf6-892e-38b1a6364122.png)

1.  将电线**C**连接到直流插孔（**G**）端口。确保红线连接到正极，黑线连接到负极。将电线**C**的另一端连接到电机驱动板（**D**）。确保红线连接到 VCC，黑线连接到 GND。用拉链扎带将直流插孔（**G**）固定到直流电机板（**E**）上。请参考以下照片以获得澄清：

![](img/a705ff16-c7c6-4fe4-b4cf-3de6501d3438.png)

这是接线图：

![](img/e4bfee31-dc2f-435d-87e2-0e76a59b343a.png)

1.  或者，您可以使用 AA 电池四组供电。确保使用相同的布线，红线连接到 VCC，黑线连接到 GND：

![](img/0ef07af9-254e-42cb-8d80-09ee55760833.png)

1.  将尾灯（**B**）穿过 LED 孔并穿过 LED 支架（**F**）：

![](img/d76c25ca-ffcf-47bd-b669-9ecaab88062e.png)

1.  将 LED 支架（**F**）推入位。如果孔太紧，使用小锉将孔稍微放大一点（LED 支架应该安装得很紧）。重复操作以安装红色尾灯：

![](img/38e3629f-3b4b-43d2-a5a7-fda32c26e074.png)

# 第 6 步 - 安装电机和轮子

在这一步中，我们将开始将零件固定到底盘上。我们将首先固定轮子支架，然后是电机。我们将在这一步中使用 3D 打印的轮子支架。

此步骤所需的零件如下图所示：

![](img/5f890aae-169b-4f3e-9bb2-02b5be256ff2.png)

以下是零件清单：

+   **A**：轮子

+   **B**：电机

+   **C**：备用轮子支架（激光切割）

+   **D**：轮子支架（3D 打印）

+   **E**：机器人车底盘（激光切割）

+   八颗 M3 10 毫米螺栓（未显示）

+   八颗 M3 30 毫米螺栓（未显示）

+   16 颗 M3 螺母（未显示）

让我们开始组装它：

1.  使用 10 毫米 M3 螺栓和螺母，将每个轮子支架（**D**）固定到底盘（**E**）上，使螺栓的头部平躺在轮子支架（**D**）中。参考以下照片以便澄清：

![](img/d8593bd5-ee03-4dd5-ad13-c7e8781d71c8.png)

1.  使用 30 毫米 M3 螺栓和 M3 螺母，通过使用轮子支架（**D**）将电机（**B**）安装到底盘（**E**）上。确保螺栓的头部平躺。参考以下照片以便澄清：

![](img/d7114241-77a4-4119-ac53-e90921cdc459.png)

1.  或者，您可以使用零件**C**代替零件**D**来安装轮子。参考以下照片以便澄清：

![](img/755bec60-4da7-45c5-89c6-34fe0c3ce9e4.png)

1.  将轮子（**A**）安装到电机（**B**）上：

![](img/de1d6e9d-4ab8-4576-8698-1c3406982398.png)

# 第 7 步 - 连接电机的电线

接下来，我们将安装电机驱动板组件并连接轮子电机的电线：

1.  首先，使用四颗 M3 10 毫米螺栓将第 5 步中的直流电机板组件固定在底盘顶部。确保将轮子电机的电线穿过中央孔。尾灯 LED 应该安装在机器人车的后部。参考以下照片以便澄清：

![](img/711e93ff-8aea-4af9-b220-4611a869bf80.png)

1.  将轮子电机的电线安装到电机驱动板的 OUT1、OUT2、OUT3 和 OUT4 端子块中。右侧电线应连接到 OUT1 和 OUT2，左侧电线应连接到 OUT3 和 OUT4。在这一点上，右侧电线连接到 OUT1 或 OUT2（或左侧电线连接到 OUT3 或 OUT4）并不重要。参考以下照片以便澄清：

![](img/2de9f44c-65e1-4aa4-bf57-dd7926e5270a.png)

# 第 8 步 - 安装摄像头支架、树莓派和 Adafruit 伺服板

机器人车开始看起来像一辆机器人车。在这一步中，我们将安装摄像头支架（或 T.A.R.A.S 的头部）和树莓派。

我们将从树莓派开始。这是我们必须在如何将树莓派和 Adafruit 伺服板安装到底盘上稍微有些创意的地方。Adafruit 伺服板是一个很棒的小板，但是套件缺少支架，无法防止板的一部分接触树莓派。我发现很难将 M3 螺栓穿过板上的安装孔。我的解决方案是使用 30 毫米的母对公支架将树莓派固定到底盘上，并使用 10 毫米的母对母支架将树莓派与 Adafruit 伺服板分开。

以下是带有一些我收集的支架的树莓派的照片：

![](img/2cb70178-957b-470f-ae89-85e59daf2dd9.png)

以下是上图中的组件：

+   **A**：15 毫米母对公尼龙支架

+   **B**：10 毫米母对母尼龙支架

+   **C**：树莓派

要创建这个电路，请按照以下步骤进行：

1.  通过将**A**的一端螺丝拧入另一个端口，创建四个 30 毫米的母对公支架。通过树莓派将**B**支架螺丝拧入**A**支架（请参考以下照片以便理解）：

![](img/93c4d596-eaf6-4ab9-aaf4-71ae304f83d8.png)

1.  使用四个 10 毫米 M3 螺栓将树莓派固定到底盘上：

![](img/322079c1-7928-4f04-a05c-da1660fa0cfb.png)

现在，让我们安装摄像头支架，连接摄像头，并安装 Adafruit 舵机板：

1.  使用四个 10 毫米 M3 螺丝和 M3 螺母将摄像头支架安装到底盘的前部（请参考以下照片以便理解）：

![](img/86ac58d3-1cee-4450-ab02-51a4fdfafbb9.png)

1.  通过摄像头模块的排线插入适当的开口到 Adafruit 舵机板（请参考以下照片以便理解）：

![](img/39566e38-0af6-4300-b297-977550b357a4.png)

1.  将 Adafruit 舵机板固定到树莓派上：

![](img/c55672a9-c12e-463e-bb57-b6bf73c896f2.png)

# 第 9 步 - 安装蜂鸣器和电压分压器

安装在底盘上的最终组件是蜂鸣器和电压分压器。我们需要电压分压器，以便我们可以从距离传感器的回波引脚向树莓派提供 3.3V。对于蜂鸣器，我们使用的是有源蜂鸣器。

有源蜂鸣器在施加直流电压时发出声音。被动蜂鸣器需要交流电压。被动蜂鸣器需要更多的编码。被动蜂鸣器更像是小音箱，因此您可以控制它们发出的声音。

![](img/0eb0829c-0678-4a30-8051-cdde32de77da.png)

以下是完成此步骤所需的组件：

+   **A**：迷你面包板

+   **B**：棕色母对母跳线

+   **C**：红色母对母跳线

+   **D**：470 欧姆电阻

+   **E**：330 欧姆电阻

+   **F**：有源蜂鸣器

按照以下步骤完成电路：

1.  为了创建电压分压器，在面包板（**A**）上将 330 欧姆（**E**）和 470 欧姆（**D**）电阻串联：

![](img/8cf75624-efcb-480b-86bc-8aa674a3697a.png)

1.  将红色跳线（**C**）连接到蜂鸣器的正极，棕色跳线（**B**）连接到另一端：

![](img/47b068ea-37a5-4de1-91b5-35967c2f7122.png)

1.  将蜂鸣器（**F**）安装在底盘上的适当孔中。使用双面泡沫胶带，将迷你面包板（**A**）粘贴到底盘的前部（请参考以下照片以便理解）：

![](img/7c9e8d6d-393d-44c9-8efc-144dca4c80dd.png)

# 第 10 步 - 连接 T.A.R.A.S

现在是你一直在等待的部分：连接所有的电线！好吧，也许整理一堆电线并理清它们并不是你的好时光。然而，稍微耐心一点，这一步骤会在你知道之前结束的。

参考以下接线图，将所有电线连接到相应的位置。我们的接线图中不包括电源和电机连接到电机驱动板，因为我们在第 7 步中已经处理了电机的接线。我已经注意到了根据它们的用途分组的电线颜色。请注意，接线图不是按比例绘制的：

![](img/a5bb3926-7d5d-4659-8245-6c8bb35c1d92.png)

要连接 T.A.R.A.S 的电线，执行以下连接：

+   从 Servo HAT 的引脚五到 L298N（电机板）的 In1

+   从 Servo HAT 的引脚六到 L298N（电机板）的 In2

+   从 Servo HAT 的引脚 27 到 L298N（电机板）的 In3

+   从 Servo HAT 的引脚 22 到 L298N（电机板）的 In4

+   从 HC-SR04（距离传感器）的 Trig 到 Servo HAT 上的引脚 17

+   从 HC-SR04（距离传感器）的回波到迷你面包板上 330 欧姆电阻的左侧

+   从 HC-SR04（距离传感器）的 VCC 到 Servo HAT 上的 5 伏特

+   从电压分压器的输出到 Servo HAT 上的引脚 18

+   从 HC-SR04 的 GND 到迷你面包板上 470 欧姆电阻的右侧

+   从迷你面包板的 GND 到 Servo HAT 上的 GND

+   从 Servo HAT 电源端子（HAT 左侧）的+5V 到电机驱动板的+5V（使用更粗的电线）

+   舵机 HAT 电源端子（HAT 左侧）的 GND 连接到电机驱动板上的 GND（使用更粗的电线）

+   从摄像头支架（水平）底部的舵机到舵机 HAT 上的零号舵机

+   从摄像头支架（倾斜）中间的舵机到舵机 HAT 上的一号舵机

+   绿色尾灯上的红线连接到舵机 HAT 上的 20 号引脚

+   绿色尾灯上的棕色线连接到舵机 HAT 上的 GND

+   红色尾灯上的红线连接到舵机 HAT 上的 21 号引脚

+   棕色线连接到舵机 HAT 上的红色尾灯接地

+   主动蜂鸣器上的红线连接到舵机 HAT 上的 12 号引脚

+   主动蜂鸣器上的棕色线连接到舵机 HAT 上的 GND

为了启动 T.A.R.A.S，我们将使用两个便携式电源。对于树莓派，我们将使用标准的 USB 便携式电源包。对于电机驱动板和舵机 HAT，我们将使用可充电的 7.4V 电池。安装电池，按照以下步骤进行：

1.  以下是我们将用于我们的机器人车的两个电池。左边的是用于树莓派的，使用 USB 到 Micro-USB 连接器。右边的是电机驱动板，使用标准的 DC 插头：

![](img/c380fe13-1eb8-4068-86b0-3bfef697dbfa.png)

1.  将剥离式粘扣条贴在两个电池和底盘上，并将电池放置在底盘上：

![](img/15fb0978-cab4-4754-89e6-6fd3b99ece93.png)

1.  经过一番必要的整理（清理电线），T.A.R.A.S 已经准备就绪：

![](img/aad6712d-7d7c-4eec-8074-0cae03d5e2dd.png)

# 学习如何控制机器人车

在第十四章中，*使用 Python 控制机器人车*，我们将开始编写代码来控制 T.A.R.A.S。在我们开始编写代码之前，看一下如何设置树莓派以访问所需的接口是个好主意。我们应该安装我们需要使用的库来创建控制代码。

# 配置我们的树莓派

为了确保我们已经启用了机器人车所需的推理，按照以下步骤进行：

1.  导航到应用程序菜单|首选项|树莓派配置

1.  单击“接口”选项卡

1.  启用摄像头、SSH 和 I2C。您可能需要重新启动树莓派：

![](img/2960257c-6d2e-4ecd-84d8-9e109d85b58a.png)

如果您尚未更改`pi`用户的默认密码，则在启用 SSH 后可能会收到有关此警告。最好更改默认密码。您可以在树莓派配置工具的系统选项卡下更改它。

# Adafruit 舵机 HAT 的 Python 库

为了访问 Adafruit 舵机 HAT，您必须下载并安装库：

1.  `git`用于从互联网下载 Adafruit 舵机 HAT 库。在 Raspbian 中打开终端并输入以下内容：

```py
sudo apt-get install -y git build-essential python-dev
```

1.  如果`git`已安装，您将收到相应的消息。否则，请继续安装`git`。

1.  在终端中输入以下内容以下载库：

```py
git clone https://github.com/adafruit/Adafruit_Python_PCA9685.git
```

1.  输入以下内容更改目录：

```py
cd Adafruit_Python_PCA9685
```

1.  使用以下命令安装库：

```py
sudo python3 setup.py install
```

1.  库已成功安装到 Thonny 的工具|管理包中。您应该能看到它在列表中：

![](img/7f1d3d07-ab79-461f-b682-777f4db5a4a8.png)

# 摘要

在本章中，我们建造了我们的机器人车 T.A.R.A.S。我们首先概述了零件，然后继续将它们组装在一起。如果您以前从未组装过机器人，那么恭喜！您已正式进入了机器人领域。接下来的路由取决于您。

在本书的其余部分，我们将编程 T.A.R.A.S 执行任务。在第十四章中，*使用 Python 控制机器人车*，T.A.R.A.S 将被要求参与秘密任务。

# 问题

1.  正确还是错误？T.A.R.A.S 代表 Technically Advanced Robots Are Superior。

1.  主动蜂鸣器和被动蜂鸣器有什么区别？

1.  正确还是错误？T.A.R.A.S 有摄像头作为眼睛。

1.  电机驱动板的作用是什么？

1.  Adafruit 舵机 HAT 的目的是什么？

1.  3D 打印一个轮子支架需要多长时间？

1.  机器人脸的目的是什么？

1.  Velcro strips are a great way to secure batteries onto the chassis.