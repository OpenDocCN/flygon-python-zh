# 低级网络设备交互

在第一章中，*TCP/IP 协议套件和 Python 概述*，我们研究了网络通信协议背后的理论和规范。我们还快速浏览了 Python 语言。在本章中，我们将开始深入探讨使用 Python 管理网络设备的不同方式，特别是我们如何使用 Python 与传统网络路由器和交换机进行程序通信。

我所说的传统网络路由器和交换机是什么意思？虽然很难想象今天出现的任何网络设备都没有用于程序通信的**应用程序接口**（**API**），但众所周知，许多在以前部署的网络设备中并不包含 API 接口。这些设备的管理方式是通过使用终端程序的**命令行接口**（**CLI**），最初是为了人类工程师而开发的。管理依赖于工程师对设备返回的数据的解释以采取适当的行动。随着网络设备数量和网络复杂性的增加，手动逐个管理它们变得越来越困难。

Python 有两个很棒的库可以帮助完成这些任务，Pexpect 和 Paramiko，以及从它们衍生出的其他库。本章将首先介绍 Pexpect，然后以 Paramiko 的示例进行讲解。一旦你了解了 Paramiko 的基础知识，就很容易扩展到 Netmiko 等更多的库。值得一提的是，Ansible（在第四章中介绍，*Python 自动化框架- Ansible 基础*，以及第五章中介绍，*Python 自动化框架-进阶*）在其网络模块中大量依赖 Paramiko。在本章中，我们将讨论以下主题：

+   命令行界面的挑战

+   构建虚拟实验室

+   Python Pexpect 库

+   Python Paramiko 库

+   Pexpect 和 Paramiko 的缺点

让我们开始吧！

# 命令行界面的挑战

在 2014 年的 Interop 博览会上，*BigSwitch Networks*的首席执行官道格拉斯·默里展示了以下幻灯片，以说明在 1993 年至 2013 年间**数据中心网络**（**DCN**）发生了什么变化：

数据中心网络变化（来源：[`www.bigswitch.com/sites/default/files/presentations/murraydouglasstartuphotseatpanel.pdf`](https://www.bigswitch.com/sites/default/files/presentations/murraydouglasstartuphotseatpanel.pdf)）

他的观点很明显：在这 20 年里，我们管理网络设备的方式几乎没有什么变化。虽然他在展示这张幻灯片时可能对现任供应商持有负面偏见，但他的观点是很有道理的。在他看来，20 年来管理路由器和交换机唯一改变的是协议从不太安全的 Telnet 变为更安全的 SSH。

正是在 2014 年左右，我们开始看到行业达成共识，即远离手动、人为驱动的 CLI，转向自动化、以计算机为中心的 API。毫无疑问，当我们进行网络设计、启动初步概念验证和首次部署拓扑时，我们仍需要直接与设备进行通信。然而，一旦我们超越了初始部署，要求是要始终可靠地进行相同的更改，使其无误，并且一遍又一遍地重复，而不让工程师分心或感到疲倦。这个要求听起来就像是计算机和我们最喜爱的语言 Python 的理想工作。

回到幻灯片，主要挑战在于路由器和管理员之间的交互。路由器将输出一系列信息，并期望管理员根据工程师对输出的解释输入一系列手动命令。例如，你必须输入`enable`才能进入特权模式，并在收到带有`#`符号的返回提示后，你再输入`configure terminal`以进入配置模式。同样的过程可以进一步扩展到接口配置模式和路由协议配置模式。这与计算机驱动的程序思维形成鲜明对比。当计算机想要完成单个任务时，比如在接口上放置 IP 地址，它希望一次性将所有信息结构化地提供给路由器，并且期望路由器给出一个`yes`或`no`的答复来指示任务的成功或失败。

Pexpect 和 Paramiko 都实现了这个解决方案，即将交互过程视为子进程，并监视进程与目标设备之间的交互。根据返回的值，父进程将决定随后的操作（如果有的话）。

# 构建虚拟实验室

在我们深入研究这些软件包之前，让我们来看看组建一个实验室以便学习的选项。正如古语所说，“熟能生巧”：我们需要一个隔离的沙盒来安全地犯错误，尝试新的做事方式，并重复一些步骤以加强在第一次尝试时不清楚的概念。安装 Python 和管理主机所需的软件包很容易，但那些我们想要模拟的路由器和交换机呢？

要组建一个网络实验室，我们基本上有两个选项，每个选项都有其优势和劣势：

+   **物理设备**：这个选项包括可以看到和触摸的物理设备。如果你足够幸运，你可能能够组建一个与你的生产环境完全相同的实验室。

+   **优势**：从实验室到生产的过渡更容易，易于经理和同事理解并触摸设备。简而言之，由于熟悉度高，对物理设备的舒适度非常高。

+   **劣势**：为实验室使用而支付设备相对昂贵。设备需要工程师花费时间来安装，并且一旦构建完成后就不太灵活。

+   **虚拟设备**：这些是实际网络设备的仿真或模拟。它们可以是供应商提供的，也可以是开源社区提供的。

+   **优势**：虚拟设备更容易设置，成本相对较低，并且可以快速更改拓扑结构。

+   **劣势**：它们通常是其物理对应物的缩减版本。有时虚拟设备和物理设备之间存在功能差距。

当然，决定使用虚拟实验室还是物理实验室是一个个人决定，需要在成本、实施的便利性和实验室与生产之间的差距之间进行权衡。在我工作过的一些环境中，当进行初步概念验证时使用虚拟实验室，而在接近最终设计时使用物理实验室。

在我看来，随着越来越多的供应商决定生产虚拟设备，虚拟实验室是学习环境中的正确选择。虚拟设备的功能差距相对较小，并且有专门的文档，特别是当虚拟实例由供应商提供时。与购买物理设备相比，虚拟设备的成本相对较低。使用虚拟设备构建的时间更快，因为它们通常只是软件程序。

对于这本书，我将使用物理和虚拟设备的组合来进行概念演示，更偏向于虚拟设备。对于我们将看到的示例，差异应该是透明的。如果虚拟和物理设备在我们的目标方面有任何已知的差异，我会确保列出它们。

在虚拟实验室方面，除了来自各种供应商的镜像，我还在使用一款来自 Cisco 的程序**Virtual Internet Routing Lab**（**VIRL**）[`learningnetworkstore.cisco.com/virtual-internet-routing-lab-virl/cisco-personal-edition-pe-20-nodes-virl-20`](https://learningnetworkstore.cisco.com/virtual-internet-routing-lab-virl/cisco-personal-edition-pe-20-nodes-virl-20)。

我想指出，读者完全可以选择是否使用这个程序。但强烈建议读者有一些实验室设备来跟随本书中的示例。

# 思科 VIRL

我记得当我第一次开始准备我的**思科认证网络专家**（**CCIE**）实验考试时，我从 eBay 购买了一些二手思科设备来学习。即使打折，每台路由器和交换机也要花费数百美元，所以为了省钱，我购买了一些上世纪 80 年代的非常过时的思科路由器（在您喜欢的搜索引擎中搜索思科 AGS 路由器，会让您大笑一番），它们明显缺乏功能和性能，即使是对实验室标准来说。尽管当我打开它们时（它们的声音很大），它们给家人带来了有趣的对话，但组装物理设备并不好玩。它们又重又笨重，连接所有的电缆很麻烦，为了引入链路故障，我会直接拔掉一根电缆。

快进几年。**Dynamip**被创建，我爱上了它创建不同网络场景的简易性。当我尝试学习一个新概念时，这尤其重要。您只需要来自 Cisco 的 IOS 镜像，一些精心构建的拓扑文件，就可以轻松构建一个虚拟网络，以便测试您的知识。我有一个完整的网络拓扑文件夹，预先保存的配置和不同版本的镜像，根据场景的需要。GNS3 前端的添加使整个设置具有美丽的 GUI 外观。使用 GNS3，您可以轻松地点击和拖放您的链接和设备；您甚至可以直接从 GNS3 设计面板打印出网络拓扑图给您的经理。唯一缺少的是该工具没有得到供应商的官方认可，因此缺乏可信度。

2015 年，Cisco 社区决定通过发布 Cisco VIRL 来满足这一需求。如果您有一台符合要求的服务器，并且愿意支付所需的年度许可证费用，这是我首选的开发和尝试大部分 Python 代码的方法，无论是为这本书还是我的自己的生产使用。

截至 2017 年 1 月 1 日，只有 20 节点个人版许可证可供购买，价格为每年 199.99 美元。

在我看来，即使需要花费一些金钱，VIRL 平台相对于其他替代方案有一些优势：

+   **易用性**：所有 IOSv、IOS-XRv、CSR100v、NX-OSv 和 ASAv 的镜像都包含在一个单独的下载中。

+   **官方**（**有点**）：尽管支持是由社区驱动的，但它在 Cisco 内部被广泛使用。由于其受欢迎程度，错误得到快速修复，新功能得到仔细记录，并且有用的知识在其用户之间广泛分享。

+   云迁移路径：当您的仿真超出您拥有的硬件能力时，比如 Cisco dCloud（[`dcloud.cisco.com/`](https://dcloud.cisco.com/)）、Packet 上的 VIRL（[`virl.cisco.com/cloud/`](http://virl.cisco.com/cloud/)）和 Cisco DevNet（[`developer.cisco.com/`](https://developer.cisco.com/)）时，该项目提供了一个逻辑的迁移路径。这是一个有时被忽视的重要特性。

+   链接和控制平面模拟：该工具可以模拟真实世界链路特性的每个链路的延迟、抖动和数据包丢失。还有一个用于外部路由注入的控制平面流量生成器。

+   **其他**：该工具提供了一些不错的功能，比如 VM Maestro 拓扑设计和模拟控制，AutoNetkit 用于自动生成配置，以及用户工作空间管理（如果服务器是共享的）。还有一些开源项目，比如 virlutils（[`github.com/CiscoDevNet/virlutils`](https://github.com/CiscoDevNet/virlutils)），由社区积极开发，以增强该工具的可用性。

在本书中，我们不会使用 VIRL 中的所有功能。但由于这是一个相对较新的工具，值得您考虑，如果您决定使用这个工具，我想提供一些我使用过的设置。

再次强调拥有一个实验室的重要性，但不一定需要是思科 VIRL 实验室。本书中提供的代码示例应该适用于任何实验室设备，只要它们运行相同的软件类型和版本。

# VIRL 提示

VIRL 网站（[`virl.cisco.com/`](http://virl.cisco.com/)）提供了大量的指导、准备和文档。我还发现 VIRL 用户社区通常能够提供快速准确的帮助。我不会重复这两个地方已经提供的信息；然而，这里是我在本书中用于实验室的一些设置：

1.  VIRL 使用两个虚拟以太网接口进行连接。第一个接口设置为主机机器的互联网连接的 NAT，第二个用于本地管理接口的连接（在下面的示例中为 VMnet2）。我使用一个具有类似网络设置的单独的虚拟机来运行我的 Python 代码，第一个主要以太网用于互联网连接，第二个以太网连接到 Vmnet2，用于实验室设备管理网络：

![](img/c82cd2d1-cdde-4f81-bc77-ae9d8fa0975c.png)

1.  VMnet2 是一个自定义网络，用于将 Ubuntu 主机与 VIRL 虚拟机连接：

![](img/85f2152f-11e7-4be2-918e-d6153657d2be.png)

1.  在拓扑设计选项中，我将管理网络选项设置为共享平面网络，以便在虚拟路由器上使用 VMnet2 作为管理网络：

![](img/02656522-3921-4e03-a513-4d10675cd081.png)

1.  在节点配置下，您可以选择静态配置管理 IP 的选项。我尝试静态设置管理 IP 地址，而不是让软件动态分配它们。这可以更确定地访问： 

![](img/ef2611d4-2e65-4fde-93d2-89b164515c8c.png)

# 思科 DevNet 和 dCloud

思科提供了另外两种非常好的、并且在撰写本文时是免费的方法，用于使用各种思科设备进行网络自动化实践。这两种工具都需要**思科连接在线（CCO）**登录。它们都非常好，尤其是价格方面（它们是免费的！）。我很难想象这些在线工具会长时间保持免费；我相信，这些工具在某个时候将需要收费或者被纳入需要收费的更大计划中。然而，在它们免费提供的时候我们可以利用它们。

第一个工具是思科 DevNet ([`developer.cisco.com/`](https://developer.cisco.com/)) 实验室，其中包括引导式学习轨迹、完整文档和远程实验室等多种好处。一些实验室是一直开放的，而另一些需要预订。实验室的可用性将取决于使用情况。如果你没有自己的实验室，这是一个很好的选择。在我使用 DevNet 的经验中，一些文档和链接已经过时，但可以很容易地获取到最新版本。在软件开发这样一个快速变化的领域，这是可以预料的。无论你是否有一个本地运行的 VIRL 主机，DevNet 都是一个你应该充分利用的工具：

![](img/687289f1-0881-4d40-a9f9-b46cb69a31bb.png)

思科的另一个在线实验室选择是[`dcloud.cisco.com/`](https://dcloud.cisco.com/)。你可以把 dCloud 看作是在其他人的服务器上运行 VIRL，而不必管理或支付这些资源。看起来思科把 dCloud 既当作一个独立的产品，又当作 VIRL 的扩展。例如，在你无法在本地运行超过几个 IOX-XR 或 NX-OS 实例的情况下，你可以使用 dCloud 来扩展你的本地实验室。这是一个相对较新的工具，但绝对值得一试：

![](img/9013d0ba-57bd-48e0-8d65-8278953469b5.png)

# GNS3

这本书和其他用途我使用了一些其他虚拟实验室。其中一个是[GNS3](https://gns3.com/)工具：

![](img/90c418fe-1abc-4b39-9bc3-38e5104ea653.png)

正如本章前面提到的，GNS3 是我们很多人用来准备认证考试和实验练习的工具。这个工具从最初的 Dynamips 的简单前端发展成了一个可行的商业产品。思科制造的工具，比如 VIRL、DevNet 和 dCloud，只包含思科技术。尽管它们提供了虚拟实验室设备与外部世界通信的方式，但并不像直接在模拟环境中拥有多供应商虚拟化设备那样简单。GNS3 是供应商中立的，可以在实验室中直接包含多供应商虚拟化平台。这通常是通过克隆镜像（比如 Arista vEOS）或者通过其他虚拟化程序直接启动网络设备镜像（比如 Juniper Olive 仿真）来实现的。有人可能会认为 GNS3 没有思科 VIRL 项目那样的广度和深度，但由于它可以运行不同版本的思科技术，所以在需要将其他供应商技术纳入实验室时，我经常使用它。

另一个得到很多好评的多供应商网络仿真环境是**Emulated Virtual Environment Next Generation (EVE-NG)**，[`www.eve-ng.net/`](http://www.eve-ng.net/)。我个人对这个工具没有太多经验，但我在这个行业的许多同事和朋友都在他们的网络实验室中使用它。

还有其他虚拟化平台，比如 Arista vEOS ([`eos.arista.com/tag/veos/`](https://eos.arista.com/tag/veos/))、Juniper vMX ([`www.juniper.net/us/en/products-services/routing/mx-series/vmx/`](http://www.juniper.net/us/en/products-services/routing/mx-series/vmx/))和 vSRX ([`www.juniper.net/us/en/products-services/security/srx-series/vsrx/`](http://www.juniper.net/us/en/products-services/security/srx-series/vsrx/))，你可以在测试中作为独立的虚拟设备使用。它们是测试特定平台功能的绝佳补充工具，比如平台上 API 版本的差异。它们通常作为付费产品在公共云提供商市场上提供，以便更容易地访问。它们通常提供与它们的物理对应产品相同的功能。

# Python Pexpect 库

Pexpect 是一个纯 Python 模块，用于生成子应用程序、控制它们并响应其输出中的预期模式。Pexpect 的工作原理类似于 Don Libes 的 Expect。Pexpect 允许您的脚本生成一个子应用程序并控制它，就像一个人在键入命令一样。Pexpect，Read the Docs: [`pexpect.readthedocs.io/en/stable/index.html`](https://pexpect.readthedocs.io/en/stable/index.html)

让我们来看看 Python Pexpect 库。与 Don Libe 的原始 Tcl Expect 模块类似，Pexpect 启动或生成另一个进程并监视它以控制交互。Expect 工具最初是为了自动化诸如 FTP、Telnet 和 rlogin 之类的交互式进程而开发的，后来扩展到包括网络自动化。与原始 Expect 不同，Pexpect 完全由 Python 编写，不需要编译 TCL 或 C 扩展。这使我们能够在我们的代码中使用熟悉的 Python 语法和其丰富的标准库。

# Pexpect 安装

由于这是我们要安装的第一个软件包，我们将同时安装`pip`工具和`pexpect`软件包。该过程非常简单：

```py
sudo apt-get install python-pip #Python2
sudo apt-get install python3-pip
sudo pip3 install pexpect
sudo pip install pexpect #Python2
```

我正在使用`pip3`来安装 Python 3 包，同时使用`pip`在 Python 2 环境中安装包。

快速测试一下确保软件包可用：

```py
>>> import pexpect
>>> dir(pexpect)
['EOF', 'ExceptionPexpect', 'Expecter', 'PY3', 
 'TIMEOUT', '__all__', '__builtins__', '__cached__', 
 '__doc__', '__file__', '__loader__', '__name__', 
 '__package__', '__path__', '__revision__', 
 '__spec__', '__version__', 'exceptions', 'expect', 
 'is_executable_file', 'pty_spawn', 'run', 'runu', 
 'searcher_re', 'searcher_string', 'spawn', 
 'spawnbase', 'spawnu', 'split_command_line', 'sys',
 'utils', 'which']
>>> 
```

# Pexpect 概述

对于我们的第一个实验，我们将构建一个简单的网络，其中有两个相连的 IOSv 设备：

！[](assets/8ae3d89a-e435-42d3-bab2-6835055f9cae.png)实验拓扑

每个设备都将在`192.16.0.x/24`范围内拥有一个环回地址，管理 IP 将在`172.16.1.x/24`范围内。VIRL 拓扑文件包含在适应书籍可下载文件中。您可以将拓扑导入到您自己的 VIRL 软件中。如果您没有 VIRL，您也可以通过使用文本编辑器打开拓扑文件来查看必要的信息。该文件只是一个 XML 文件，每个节点的信息都在`node`元素下：

！[](assets/15ca7c31-52f3-4c40-a953-ea2b7a79cb45.png)实验节点信息

设备准备就绪后，让我们看看如果您要 Telnet 到设备中，您将如何与路由器进行交互：

```py
echou@ubuntu:~$ telnet 172.16.1.20
Trying 172.16.1.20...
Connected to 172.16.1.20.
Escape character is '^]'.
<skip>
User Access Verification

Username: cisco
Password:
```

我使用 VIRL AutoNetkit 自动生成路由器的初始配置，生成了默认用户名`cisco`和密码`cisco`。请注意，由于配置中分配的特权，用户已经处于特权模式下：

```py
iosv-1#sh run | i cisco
enable password cisco
username cisco privilege 15 secret 5 $1$Wiwq$7xt2oE0P9ThdxFS02trFw.
 password cisco
 password cisco
iosv-1#
```

自动配置还为 Telnet 和 SSH 生成了`vty`访问：

```py
line vty 0 4
 exec-timeout 720 0
 password cisco
 login local
 transport input telnet ssh
```

让我们看一个使用 Python 交互式 shell 的 Pexpect 示例：

```py
Python 3.5.2 (default, Nov 17 2016, 17:05:23)
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import pexpect
>>> child = pexpect.spawn('telnet 172.16.1.20')
>>> child.expect('Username')
0
>>> child.sendline('cisco')
6
>>> child.expect('Password')
0
>>> child.sendline('cisco')
6
>>> child.expect('iosv-1#')
0
>>> child.sendline('show version | i V')
19
>>> child.expect('iosv-1#')
0
>>> child.before
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'
>>> child.sendline('exit')
5
>>> exit()
```

从 Pexpect 版本 4.0 开始，您可以在 Windows 平台上运行 Pexpect。但是，正如 Pexpect 文档中所指出的，目前应该将在 Windows 上运行 Pexpect 视为实验性的。

在上一个交互式示例中，Pexpect 生成了一个子进程并以交互方式监视它。示例中显示了两个重要的方法，`expect()`和`sendline()`。`expect()`行指示 Pexpect 进程寻找的字符串，作为返回的字符串被视为完成的指示器。这是预期的模式。在我们的示例中，当返回主机名提示（`iosv-1#`）时，我们知道路由器已经向我们发送了所有信息。`sendline()`方法指示应将哪些单词作为命令发送到远程设备。还有一个名为`send()`的方法，但`sendline()`包括一个换行符，类似于在上一个 telnet 会话中按下*Enter*键。从路由器的角度来看，这就像有人从终端键入文本一样。换句话说，我们正在欺骗路由器，让它们认为它们正在与人类进行交互，而实际上它们正在与计算机进行通信。

`before`和`after`属性将设置为子应用程序打印的文本。`before`属性将设置为子应用程序打印的文本，直到预期的模式。`after`字符串将包含由预期模式匹配的文本。在我们的情况下，`before`文本将设置为两个预期匹配（`iosv-1#`）之间的输出，包括`show version`命令。`after`文本是路由器主机名提示符：

```py
>>> child.sendline('show version | i V')
19
>>> child.expect('iosv-1#')
0
>>> child.before
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'
>>> child.after
b'iosv-1#'
```

如果您期望错误的术语会发生什么？例如，如果在生成子应用程序后输入`username`而不是`Username`，那么 Pexpect 进程将从子进程中寻找一个`username`字符串。在这种情况下，Pexpect 进程将会挂起，因为路由器永远不会返回`username`这个词。会话最终会超时，或者您可以通过*Ctrl* + *C*手动退出。

`expect()`方法等待子应用程序返回给定的字符串，因此在前面的示例中，如果您想要适应小写和大写的`u`，您可以使用以下术语：

```py
>>> child.expect('[Uu]sername')
```

方括号作为`or`操作，告诉子应用程序期望小写或大写的`u`后跟字符串`sername`。我们告诉进程的是我们将接受`Username`或`username`作为预期字符串。

有关 Python 正则表达式的更多信息，请访问[`docs.python.org/3.5/library/re.html`](https://docs.python.org/3.5/library/re.html)。

`expect()`方法也可以包含一个选项列表，而不仅仅是一个单独的字符串；这些选项本身也可以是正则表达式。回到前面的示例，您可以使用以下选项列表来适应两种不同的可能字符串：

```py
>>> child.expect(['Username', 'username'])
```

一般来说，当您可以在正则表达式中适应不同的主机名时，使用单个`expect`字符串的正则表达式，而如果您需要捕获路由器完全不同的响应，例如密码拒绝，那么使用可能的选项。例如，如果您对登录使用了几个不同的密码，您希望捕获`％Login invalid`以及设备提示。

Pexpect 正则表达式和 Python 正则表达式之间的一个重要区别是，Pexpect 匹配是非贪婪的，这意味着在使用特殊字符时，它们将尽可能少地匹配。因为 Pexpect 在流上执行正则表达式，所以您不能向前查看，因为生成流的子进程可能尚未完成。这意味着特殊的美元符号字符`$`通常匹配行尾是无用的，因为`.+`总是不会返回任何字符，而`.*`模式将尽可能少地匹配。一般来说，记住这一点，并尽可能具体地匹配`expect`字符串。

让我们考虑以下情景：

```py
>>> child.sendline('show run | i hostname')
22
>>> child.expect('iosv-1')
0
>>> child.before
b'show run | i hostnamernhostname '
>>>
```

嗯...这里有点不对劲。与之前的终端输出进行比较；您期望的输出应该是`hostname iosv-1`：

```py
iosv-1#show run | i hostname
hostname iosv-1
iosv-1#
```

仔细查看预期的字符串将会揭示错误。在这种情况下，我们忘记了在`iosv-1`主机名后面加上`#`号。因此，子应用程序将返回字符串的第二部分视为预期字符串：

```py
>>> child.sendline('show run | i hostname')
22
>>> child.expect('iosv-1#')
0
>>> child.before
b'show run | i hostnamernhostname iosv-1rn'
>>>
```

通过几个示例后，您可以看到 Pexpect 的使用模式。用户可以规划 Pexpect 进程和子应用程序之间的交互序列。通过一些 Python 变量和循环，我们可以开始构建一个有用的程序，帮助我们收集信息并对网络设备进行更改。

# 我们的第一个 Pexpect 程序

我们的第一个程序`chapter2_1.py`扩展了上一节的内容，并添加了一些额外的代码：

```py
     #!/usr/bin/python3

     import pexpect

     devices = {'iosv-1': {'prompt': 'iosv-1#', 'ip': '172.16.1.20'}, 'iosv-2': {'prompt': 'iosv-2#', 'ip': '172.16.1.21'}}
     username = 'cisco'
     password = 'cisco'

     for device in devices.keys():
         device_prompt = devices[device]['prompt']
         child = pexpect.spawn('telnet ' + devices[device]['ip'])
         child.expect('Username:')
         child.sendline(username)
         child.expect('Password:')
         child.sendline(password)
         child.expect(device_prompt)
         child.sendline('show version | i V')
         child.expect(device_prompt)
         print(child.before)
         child.sendline('exit')
```

我们在第 5 行使用了嵌套字典：

```py
       devices = {'iosv-1': {'prompt': 'iosv-1#', 'ip': 
      '172.16.1.20'}, 'iosv-2': {'prompt': 'iosv-2#', 
      'ip': '172.16.1.21'}}
```

嵌套字典允许我们使用适当的 IP 地址和提示符号引用相同的设备（例如`iosv-1`）。然后我们可以在循环后面使用这些值进行`expect()`方法。

输出在屏幕上打印出每个设备的`show version | i V`输出：

```py
 $ python3 chapter2_1.py
 b'show version | i VrnCisco IOS Software, IOSv
 Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, 
 RELEASE SOFTWARE (fc2)rnProcessor board ID 
 9MM4BI7B0DSWK40KV1IIRrn'
 b'show version | i VrnCisco IOS Software, IOSv 
 Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T,
 RELEASE SOFTWARE (fc2)rn'
```

# 更多 Pexpect 功能

在本节中，我们将看看更多 Pexpect 功能，这些功能在某些情况下可能会派上用场。

如果您的远程设备链接速度慢或快，默认的`expect()`方法超时时间为 30 秒，可以通过`timeout`参数增加或减少：

```py
>>> child.expect('Username', timeout=5)
```

您可以选择使用`interact()`方法将命令传递回用户。当您只想自动化初始任务的某些部分时，这是很有用的：

```py
>>> child.sendline('show version | i V')
19
>>> child.expect('iosv-1#')
0
>>> child.before
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'
>>> child.interact()
iosv-1#show run | i hostname
hostname iosv-1
iosv-1#exit
Connection closed by foreign host.
>>>
```

通过以字符串格式打印`child.spawn`对象，您可以获得有关该对象的大量信息：

```py
>>> str(child)
"<pexpect.pty_spawn.spawn object at 0x7fb01e29dba8>ncommand: /usr/bin/telnetnargs: ['/usr/bin/telnet', '172.16.1.20']nsearcher: Nonenbuffer (last 100 chars): b''nbefore (last 100 chars): b'NTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'nafter: b'iosv-1#'nmatch: <_sre.SRE_Match object; span=(164, 171), match=b'iosv-1#'>nmatch_index: 0nexitstatus: 1nflag_eof: Falsenpid: 2807nchild_fd: 5nclosed: Falsentimeout: 30ndelimiter: <class 'pexpect.exceptions.EOF'>nlogfile: Nonenlogfile_read: Nonenlogfile_send: Nonenmaxread: 2000nignorecase: Falsensearchwindowsize: Nonendelaybeforesend: 0.05ndelayafterclose: 0.1ndelayafterterminate: 0.1"
>>>
```

Pexpect 最有用的调试工具是将输出记录在文件中：

```py
>>> child = pexpect.spawn('telnet 172.16.1.20')
>>> child.logfile = open('debug', 'wb')
```

使用`child.logfile = open('debug', 'w')`来代替 Python 2。Python 3 默认使用字节字符串。有关 Pexpect 功能的更多信息，请查看[`pexpect.readthedocs.io/en/stable/api/index.html`](https://pexpect.readthedocs.io/en/stable/api/index.html)。

# Pexpect 和 SSH

如果您尝试使用先前的 Telnet 示例并将其插入 SSH 会话，您可能会对体验感到非常沮丧。您始终必须在会话中包含用户名，回答`ssh`新密钥问题，以及更多琐碎的任务。有许多方法可以使 SSH 会话工作，但幸运的是，Pexpect 有一个名为`pxssh`的子类，专门用于建立 SSH 连接。该类添加了登录、注销和处理`ssh`登录过程中不同情况的各种棘手事务的方法。这些过程大多数情况下是相同的，除了`login()`和`logout()`：

```py
>>> from pexpect import pxssh
>>> child = pxssh.pxssh()
>>> child.login('172.16.1.20', 'cisco', 'cisco', auto_prompt_reset=False)
True
>>> child.sendline('show version | i V')
19
>>> child.expect('iosv-1#')
0
>>> child.before
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'
>>> child.logout()
>>>
```

注意`login()`方法中的`auto_prompt_reset=False`参数。默认情况下，`pxssh`使用 Shell 提示来同步输出。但由于它在大多数 bash 或 CSH 中使用 PS1 选项，它们将在 Cisco 或其他网络设备上出错。

# 为 Pexpect 整合各种功能

作为最后一步，让我们将到目前为止学到的关于 Pexpect 的一切放入脚本中。将代码放入脚本中可以更容易地在生产环境中使用，并且更容易与同事共享。我们将编写第二个脚本`chapter2_2.py`。

您可以从书的 GitHub 存储库[`github.com/PacktPublishing/Mastering-Python-Networking-second-edition`](https://github.com/PacktPublishing/Mastering-Python-Networking-second-edition)下载脚本，以及查看由脚本生成的输出作为命令的结果。参考以下代码：

```py
  #!/usr/bin/python3

  import getpass
  from pexpect import pxssh

  devices = {'iosv-1': {'prompt': 'iosv-1#', 'ip': '172.16.1.20'},
  'iosv-2': {'prompt': 'iosv-2#', 'ip': '172.16.1.21'}}
  commands = ['term length 0', 'show version', 'show run']

  username = input('Username: ')
  password = getpass.getpass('Password: ')

  # Starts the loop for devices
  for device in devices.keys():
      outputFileName = device + '_output.txt'
      device_prompt = devices[device]['prompt']
      child = pxssh.pxssh()
      child.login(devices[device]['ip'], username.strip(), password.strip(), auto_promp t_reset=False)
      # Starts the loop for commands and write to output
      with open(outputFileName, 'wb') as f:
          for command in commands:
              child.sendline(command)
              child.expect(device_prompt)
              f.write(child.before)

      child.logout()
```

该脚本从我们的第一个 Pexpect 程序进一步扩展，具有以下附加功能：

+   它使用 SSH 而不是 Telnet

+   通过将命令转换为列表（第 8 行）并循环执行命令（从第 20 行开始），它支持多个命令而不仅仅是一个命令

+   它提示用户输入用户名和密码，而不是在脚本中硬编码它们

+   它将输出写入两个文件，`iosv-1_output.txt`和`ios-2_output.txt`，以便进一步分析

对于 Python 2，使用`raw_input()`而不是`input()`来进行用户名提示。此外，使用`w`而不是`wb`作为文件模式。

# Python Paramiko 库

Paramiko 是 SSHv2 协议的 Python 实现。就像 Pexpect 的`pxssh`子类一样，Paramiko 简化了主机和远程设备之间的 SSHv2 交互。与`pxssh`不同，Paramiko 仅专注于 SSHv2，不支持 Telnet。它还提供客户端和服务器操作。

Paramiko 是高级自动化框架 Ansible 用于其网络模块的低级 SSH 客户端。我们将在后面的章节中介绍 Ansible。让我们来看看 Paramiko 库。

# Paramiko 的安装

使用 Python `pip`安装 Paramiko 非常简单。但是，它对 cryptography 库有严格的依赖。该库为 SSH 协议提供了基于 C 的低级加密算法。

Windows、Mac 和其他 Linux 版本的安装说明可以在[`cryptography.io/en/latest/installation/`](https://cryptography.io/en/latest/installation/)找到。

我们将在接下来的输出中展示 Ubuntu 16.04 虚拟机上 Paramiko 的安装。以下输出显示了安装步骤，以及 Paramiko 成功导入 Python 交互提示符。

如果您使用的是 Python 2，请按照以下步骤。我们将尝试在交互提示符中导入库，以确保库可以使用：

```py
sudo apt-get install build-essential libssl-dev libffi-dev python-dev
sudo pip install cryptography
sudo pip install paramiko
$ python
Python 2.7.12 (default, Nov 19 2016, 06:48:10)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import paramiko
>>> exit()
```

如果您使用的是 Python 3，请参考以下命令行安装依赖项。安装后，我们将导入库以确保它已正确安装：

```py
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev
sudo pip3 install cryptography
sudo pip3 install paramiko
$ python3
Python 3.5.2 (default, Nov 17 2016, 17:05:23)
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import paramiko
>>>
```

# Paramiko 概述

让我们来看一个使用 Python 3 交互式 shell 的快速 Paramiko 示例：

```py
>>> import paramiko, time
>>> connection = paramiko.SSHClient()
>>> connection.set_missing_host_key_policy(paramiko.AutoAddPolicy())
>>> connection.connect('172.16.1.20', username='cisco', password='cisco', look_for_keys=False, allow_agent=False)
>>> new_connection = connection.invoke_shell()
>>> output = new_connection.recv(5000)
>>> print(output)
b"rn**************************************************************************rn* IOSv is strictly limited to use for evaluation, demonstration and IOS *rn* education. IOSv is provided as-is and is not supported by Cisco's *rn* Technical Advisory Center. Any use or disclosure, in whole or in part, *rn* of the IOSv Software or Documentation to any third party for any *rn* purposes is expressly prohibited except as otherwise authorized by *rn* Cisco in writing. *rn**************************************************************************rniosv-1#"
>>> new_connection.send("show version | i Vn")
19
>>> time.sleep(3)
>>> output = new_connection.recv(5000)
>>> print(output)
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrniosv-1#'
>>> new_connection.close()
>>>
```

`time.sleep()`函数插入时间延迟，以确保所有输出都被捕获。这在网络连接较慢或设备繁忙时特别有用。这个命令不是必需的，但根据您的情况，建议使用。

即使您是第一次看到 Paramiko 操作，Python 及其清晰的语法意味着您可以对程序尝试做什么有一个相当好的猜测：

```py
>>> import paramiko
>>> connection = paramiko.SSHClient()
>>> connection.set_missing_host_key_policy(paramiko.AutoAddPolicy())
>>> connection.connect('172.16.1.20', username='cisco', password='cisco', look_for_keys=False, allow_agent=False)
```

前四行创建了 Paramiko 的`SSHClient`类的实例。下一行设置了客户端在 SSH 服务器的主机名（在本例中为`iosv-1`）不在系统主机密钥或应用程序密钥中时应使用的策略。在我们的情况下，我们将自动将密钥添加到应用程序的`HostKeys`对象中。此时，如果您登录路由器，您将看到 Paramiko 的额外登录会话：

```py
iosv-1#who
 Line User Host(s) Idle Location
*578 vty 0 cisco idle 00:00:00 172.16.1.1
 579 vty 1 cisco idle 00:01:30 172.16.1.173
Interface User Mode Idle Peer Address
iosv-1#
```

接下来的几行调用连接的新交互式 shell，并重复发送命令和检索输出的模式。最后，我们关闭连接。

一些之前使用过 Paramiko 的读者可能对`exec_command()`方法比调用 shell 更熟悉。为什么我们需要调用交互式 shell 而不是直接使用`exec_command()`呢？不幸的是，在 Cisco IOS 上，`exec_command()`只允许一个命令。考虑以下使用`exec_command()`进行连接的示例：

```py
>>> connection.connect('172.16.1.20', username='cisco', password='cisco', look_for_keys=False, allow_agent=False)
>>> stdin, stdout, stderr = connection.exec_command('show version | i V')
>>> stdout.read()
b'Cisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrn'
>>> 
```

一切都很顺利；但是，如果您查看 Cisco 设备上的会话数量，您会注意到连接被 Cisco 设备中断，而您并没有关闭连接：

```py
iosv-1#who
 Line User Host(s) Idle Location
*578 vty 0 cisco idle 00:00:00 172.16.1.1
Interface User Mode Idle Peer Address
iosv-1#
```

因为 SSH 会话不再活动，如果您想向远程设备发送更多命令，`exec_command()`将返回错误：

```py
>>> stdin, stdout, stderr = connection.exec_command('show version | i V')
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
 File "/usr/local/lib/python3.5/dist-packages/paramiko/client.py", line 435, in exec_command
 chan = self._transport.open_session(timeout=timeout)
 File "/usr/local/lib/python3.5/dist-packages/paramiko/transport.py", line 711, in open_session
 timeout=timeout)
 File "/usr/local/lib/python3.5/dist-packages/paramiko/transport.py", line 795, in open_channel
 raise SSHException('SSH session not active')
paramiko.ssh_exception.SSHException: SSH session not active
>>>
```

Kirk Byers 的 Netmiko 库是一个开源的 Python 库，简化了对网络设备的 SSH 管理。要了解更多信息，请查看这篇文章[`pynet.twb-tech.com/blog/automation/netmiko.html`](https://pynet.twb-tech.com/blog/automation/netmiko.html)，以及源代码[`github.com/ktbyers/netmiko`](https://github.com/ktbyers/netmiko)。

如果您不清除接收到的缓冲区，会发生什么？输出将继续填充缓冲区并覆盖它：

```py
>>> new_connection.send("show version | i Vn")
19
>>> new_connection.send("show version | i Vn")
19
>>> new_connection.send("show version | i Vn")
19
>>> new_connection.recv(5000)
b'show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrniosv-1#show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrniosv-1#show version | i VrnCisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2)rnProcessor board ID 9MM4BI7B0DSWK40KV1IIRrniosv-1#'
>>>
```

为了保持确定性输出的一致性，我们将在每次执行命令时从缓冲区中检索输出。

# 我们的第一个 Paramiko 程序

我们的第一个程序将使用与我们组合的 Pexpect 程序相同的一般结构。我们将循环遍历设备和命令列表，同时使用 Paramiko 而不是 Pexpect。这将使我们很好地比较 Paramiko 和 Pexpect 之间的差异。

如果您还没有这样做，您可以从书的 GitHub 存储库[`github.com/PacktPublishing/Mastering-Python-Networking-second-edition`](https://github.com/PacktPublishing/Mastering-Python-Networking)下载代码`chapter2_3.py`。我将在这里列出显著的区别：

```py
devices = {'iosv-1': {'ip': '172.16.1.20'}, 'iosv-2': {'ip': '172.16.1.21'}}
```

我们不再需要使用 Paramiko 来匹配设备提示；因此，设备字典可以简化：

```py
commands = ['show version', 'show run']
```

Paramiko 中没有 sendline 的等价物；相反，我们需要在每个命令中手动包含换行符：

```py
def clear_buffer(connection):
    if connection.recv_ready():
        return connection.recv(max_buffer)
```

我们包含了一个新的方法来清除发送命令的缓冲区，比如`terminal length 0`或`enable`，因为我们不需要这些命令的输出。我们只是想清除缓冲区并进入执行提示符。这个函数稍后将在循环中使用，比如脚本的第 25 行：

```py
output = clear_buffer(new_connection)
```

程序的其余部分应该是相当容易理解的，类似于我们在本章中看到的内容。我想指出的最后一件事是，由于这是一个交互式程序，我们在远程设备上放置了一些缓冲区，并等待命令在远程设备上完成后再检索输出。

```py
time.sleep(2)
```

在清除缓冲区之后，在执行命令之间，我们将等待两秒。这将给设备足够的时间来响应，如果它很忙的话。

# 更多 Paramiko 功能

我们将在本书的后面部分再次看到 Paramiko，当我们讨论 Ansible 时，Paramiko 是许多网络模块的基础传输。在本节中，我们将看一下 Paramiko 的一些其他功能。

# Paramiko 用于服务器

Paramiko 也可以用于通过 SSHv2 管理服务器。让我们看一个使用 Paramiko 管理服务器的例子。我们将使用基于密钥的身份验证进行 SSHv2 会话。

在这个例子中，我使用了与目标服务器相同的虚拟机上的另一个 Ubuntu 虚拟机。您也可以使用 VIRL 模拟器上的服务器或者公共云提供商之一的实例，比如亚马逊 AWS EC2。

我们将为 Paramiko 主机生成一个公私钥对：

```py
ssh-keygen -t rsa
```

这个命令默认会生成一个名为`id_rsa.pub`的公钥，作为用户主目录`~/.ssh`下的公钥，以及一个名为`id_rsa`的私钥。对待私钥的注意力应与您不想与任何其他人分享的私人密码一样。您可以将公钥视为标识您身份的名片。使用私钥和公钥，消息将在本地由您的私钥加密，然后由远程主机使用公钥解密。我们应该将公钥复制到远程主机。在生产环境中，我们可以通过使用 USB 驱动器进行离线复制；在我们的实验室中，我们可以简单地将公钥复制到远程主机的`~/.ssh/authorized_keys`文件中。打开远程服务器的终端窗口，这样您就可以粘贴公钥。

使用 Paramiko 将`~/.ssh/id_rsa`的内容复制到您的管理主机上：

```py
<Management Host with Pramiko>$ cat ~/.ssh/id_rsa.pub
ssh-rsa <your public key> echou@pythonicNeteng
```

然后，将其粘贴到远程主机的`user`目录下；在这种情况下，我在双方都使用`echou`：

```py
<Remote Host>$ vim ~/.ssh/authorized_keys
ssh-rsa <your public key> echou@pythonicNeteng
```

您现在可以使用 Paramiko 来管理远程主机。请注意，在这个例子中，我们将使用私钥进行身份验证，以及`exec_command()`方法来发送命令：

```py
Python 3.5.2 (default, Nov 17 2016, 17:05:23)
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import paramiko
>>> key = paramiko.RSAKey.from_private_key_file('/home/echou/.ssh/id_rsa')
>>> client = paramiko.SSHClient()
>>> client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
>>> client.connect('192.168.199.182', username='echou', pkey=key)
>>> stdin, stdout, stderr = client.exec_command('ls -l')
>>> stdout.read()
b'total 44ndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Desktopndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Documentsndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Downloadsn-rw-r--r-- 1 echou echou 8980 Jan 7 10:03 examples.desktopndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Musicndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Picturesndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Publicndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Templatesndrwxr-xr-x 2 echou echou 4096 Jan 7 10:14 Videosn'
>>> stdin, stdout, stderr = client.exec_command('pwd')
>>> stdout.read()
b'/home/echoun'
>>> client.close()
>>>
```

请注意，在服务器示例中，我们不需要创建交互式会话来执行多个命令。您现在可以关闭远程主机的 SSHv2 配置中基于密码的身份验证，以实现更安全的基于密钥的身份验证，并启用自动化。一些网络设备，如 Cumulus 和 Vyatta 交换机，也支持基于密钥的身份验证。

# 为 Paramiko 整合各种功能

我们几乎已经到了本章的结尾。在这最后一节中，让我们使 Paramiko 程序更具重用性。我们现有脚本的一个缺点是：每次我们想要添加或删除主机，或者每当我们需要更改要在远程主机上执行的命令时，我们都需要打开脚本。这是因为主机和命令信息都是静态输入到脚本中的。硬编码主机和命令更容易出错。此外，如果你要把脚本传给同事，他们可能不太愿意在 Python、Paramiko 或 Linux 中工作。

通过将主机和命令文件都作为脚本的参数读入，我们可以消除一些这些顾虑。用户（包括未来的你）可以在需要更改主机或命令时简单地修改这些文本文件。

我们已经在名为`chapter2_4.py`的脚本中引入了更改。

我们没有将命令硬编码，而是将命令分成一个单独的`commands.txt`文件。到目前为止，我们一直在使用 show 命令；在这个例子中，我们将进行配置更改。特别是，我们将日志缓冲区大小更改为`30000`字节：

```py
$ cat commands.txt
config t
logging buffered 30000
end
copy run start
```

设备的信息被写入`devices.json`文件。我们选择 JSON 格式来存储设备信息，因为 JSON 数据类型可以很容易地转换为 Python 字典数据类型：

```py
$ cat devices.json
{
 "iosv-1": {"ip": "172.16.1.20"},
 "iosv-2": {"ip": "172.16.1.21"}
}
```

在脚本中，我们做出了以下更改：

```py
  with open('devices.json', 'r') as f:
      devices = json.load(f)

  with open('commands.txt', 'r') as f:
      commands = [line for line in f.readlines()]
```

以下是脚本执行的简化输出：

```py
$ python3 chapter2_4.py
Username: cisco
Password:
b'terminal length 0rniosv-2#config trnEnter configuration commands, one per line. End with CNTL/Z.rniosv-2(config)#'
b'logging buffered 30000rniosv-2(config)#'
...
```

快速检查确保更改已经在`running-config`和`startup-config`中生效：

```py
iosv-1#sh run | i logging
logging buffered 30000
iosv-1#sh start | i logging
logging buffered 30000
```

```py
iosv-2#sh run | i logging
logging buffered 30000
iosv-2#sh start | i logging
logging buffered 30000

```

# 展望未来

就自动化网络使用 Python 而言，本章我们已经取得了相当大的进步。然而，我们使用的方法感觉有点像自动化的变通方法。我们试图欺骗远程设备，让它们认为它们在与另一端的人类进行交互。

# 与其他工具相比，Pexpect 和 Paramiko 的缺点

到目前为止，我们的方法最大的缺点是远程设备没有返回结构化数据。它们返回的数据适合在终端上显示并由人类解释，而不是由计算机程序。人眼可以轻松解释空格，而计算机只能看到回车符。

我们将在接下来的章节中看到更好的方法。作为第三章 *API 和意图驱动的网络*的序曲，让我们讨论幂等性的概念。

# 网络设备交互的幂等性

**幂等性**一词在不同的语境中有不同的含义。但在本书的语境中，该术语意味着当客户端对远程设备进行相同的调用时，结果应始终相同。我相信我们都同意这一点。想象一种情况，每次执行相同的脚本时，你都会得到不同的结果。我觉得那种情况非常可怕。如果是这种情况，你怎么能相信你的脚本呢？这将使我们的自动化工作变得毫无意义，因为我们需要准备处理不同的返回结果。

由于 Pexpect 和 Paramiko 正在交互式地发送一系列命令，非幂等交互的机会更高。回到需要从返回结果中筛选有用元素的事实，差异的风险更高。在我们编写脚本和脚本执行 100 次之间，远程端的某些内容可能已经发生了变化。例如，如果供应商在发布之间更改了屏幕输出，而我们没有更新脚本，脚本可能会出错。

如果我们需要依赖脚本进行生产，我们需要尽可能使脚本具有幂等性。

# 糟糕的自动化会加速糟糕的事情发生

糟糕的自动化会让你更快地刺瞎自己的眼睛，就是这么简单。计算机在执行任务时比我们人类工程师快得多。如果我们用相同的操作程序由人类和脚本执行，脚本会比人类更快地完成，有时甚至没有在程序之间建立良好的反馈循环的好处。互联网上充满了当有人按下*Enter*键后立即后悔的恐怖故事。

我们需要确保糟糕的自动化脚本搞砸事情的机会尽可能小。我们都会犯错；在进行任何生产工作之前仔细测试您的脚本和小的影响范围是确保您能在错误发生之前捕捉到它的关键。

# 总结

在本章中，我们介绍了与网络设备直接通信的低级方式。如果没有一种以编程方式与网络设备通信并对其进行更改的方法，就不可能实现自动化。我们看了 Python 中的两个库，它们允许我们管理本来应该由 CLI 管理的设备。虽然有用，但很容易看出这个过程可能有些脆弱。这主要是因为所涉及的网络设备本来是为人类而不是计算机管理而设计的。

在第三章中，*API 和意图驱动的网络*，我们将看看支持 API 和意图驱动网络的网络设备。
