# 前言

响应式图形用户界面（GUI）帮助您与应用程序交互，提高用户体验，并增强应用程序的效率。使用 Python，您将可以访问精心设计的 GUI 框架，可以用来构建与众不同的交互式 GUI。

本学习路径首先介绍了 Tkinter 和 PyQt，然后引导您通过应用程序开发过程。随着您通过添加更多小部件扩展您的 GUI，您将使用增强其功能的网络、数据库和图形库。您还将学习如何连接到外部数据库和网络资源，测试您的代码，并使用异步编程来最大化性能。在后面的章节中，您将了解如何使用 Tkinter 和 Qt5 的跨平台功能来保持跨平台兼容性。您将能够模仿平台本地的外观和感觉，并构建可在流行的计算平台上部署的可执行文件。

在学习路径结束时，您将具备设计和构建高端 GUI 应用程序的技能和信心，可以解决现实世界中的问题。

本学习路径包括以下 Packt 产品的内容：

+   *Python GUI Programming with Tkinter* by Alan D. Moore

+   *Qt5 Python GUI Programming Cookbook* by B. M. Harwani

# 本书适合对象

如果您是一名中级 Python 程序员，希望通过使用 PyQT 和 Tkinter 在 Python 中编写强大的 GUI 来增强您的编码技能，那么这对您来说是一个理想的学习路径。对 Python 语言的深入理解是理解本书中解释的概念的必要条件。

# 本书涵盖的内容

*第一章*，*Tkinter 简介*，向您介绍了 Tkinter 库的基础知识，并带您创建一个 Hello World 应用程序。它还将向您介绍 IDLE 作为 Tkinter 应用程序的示例。

*第二章*，*使用 Tkinter 设计 GUI 应用程序*，介绍了将一组用户需求转化为我们可以实现的设计过程。

*第三章*，*使用 Tkinter 和 ttk 小部件创建基本表单*，向您展示如何创建一个基本的数据输入表单，将数据追加到 CSV 文件中。

*第四章*，*使用验证和自动化减少用户错误*，演示了如何自动填充和验证我们表单输入中的数据。

*第五章*，*规划我们应用程序的扩展*，让您了解如何将一个小脚本分解为多个文件，并构建一个可以导入的 Python 模块。它还包含一些关于如何管理更大代码库的一般建议。

*第六章*，*使用 Menu 和 Tkinter 对话框创建菜单*，概述了使用 Tkinter 创建主菜单。它还将展示使用几种内置对话框类型来实现常见菜单功能。

*第七章*，*使用 Treeview 导航记录*，详细介绍了使用 Tkinter Treeview 构建记录导航系统，并将我们的应用程序从仅追加转换为具有完整读取、写入和更新功能。

*第八章*，*使用样式和主题改善外观*，告诉您如何更改应用程序的颜色、字体和小部件样式，以及如何使用它们使您的应用程序更易用。

*第九章*，*使用 unittest 创建自动化测试*，讨论了如何使用自动化单元测试和集成测试来验证您的代码。

第十章《使用 SQL 改进数据存储》带您了解如何将我们的应用程序从 CSV 平面文件转换为 SQL 数据存储。您将学习有关 SQL 和关系数据模型的所有知识。

第十一章《连接到云》介绍了如何使用云服务，如 Web 服务和 FTP 来下载和上传数据。

第十二章《使用 Canvas 小部件可视化数据》教会您如何使用 Tkinter 的 Canvas 小部件来创建可视化和动画。

第十三章《使用 Qt 组件创建用户界面》教会您如何使用 Qt Designer 的某些基本小部件，以及如何显示欢迎消息和用户名。您将学会使用单选按钮从几个选项中选择一个选项，并通过复选框选择多个选项。

第十四章《事件处理-信号和槽》介绍了如何在任何小部件上发生特定事件时执行特定任务，以及如何从一个行编辑小部件复制和粘贴文本到另一个小部件，转换数据类型并制作一个小型计算器，以及使用微调框、滚动条和滑块。您还将学会使用列表小部件执行多个任务。

第十五章《理解面向对象编程概念》讨论了面向对象编程概念，如如何在 GUI 应用程序中使用类、单继承、多级继承和多重继承。

第十六章《理解对话框》探讨了使用特定对话框，每个对话框用于获取不同类型的信息。您还将学会使用输入对话框从用户那里获取输入。

第十七章《理解布局》解释了如何通过使用水平布局、垂直布局和不同布局来水平、垂直地排列小部件，以及如何使用表单布局在两列布局中排列小部件。

第十八章《网络和管理大型文档》演示了如何制作一个小型浏览器，建立客户端和服务器之间的连接，创建一个可停靠和可浮动的登录表单，并使用 MDI 管理多个文档。此外，您还将学会使用选项卡小部件在各个部分显示信息，以及如何创建一个自定义菜单栏，在选择特定菜单项时调用不同的图形工具。

第十九章《数据库处理》概述了如何管理 SQLite 数据库以保存未来使用的信息。利用所学知识，您将学会制作一个登录表单，检查用户的电子邮件地址和密码是否正确。

第二十章《使用图形》解释了如何在应用程序中显示特定的图形。您还将学习如何创建自己的工具栏，其中包含可用于绘制不同图形的特定工具。

第二十一章《实现动画》介绍了如何显示 2D 图形图像，使球在点击按钮时向下移动，制作一个弹跳的球，以及根据指定曲线使球动画化。

第二十二章《使用 Google 地图》展示了如何使用 Google API 显示位置和其他信息。您将学会根据输入的经度和纬度值计算两个位置之间的距离，并在 Google 地图上显示位置。

# 为了充分利用本书

本书期望您了解 Python 3 的基础知识。您应该知道如何使用内置类型和函数编写和运行简单脚本，如何定义自己的函数和类，以及如何从标准库导入模块。

您可以在 Windows、macOS、Linux 甚至 BSD 上使用本书。确保您已安装 Python 3 和 Tcl/Tk，并且有一个您熟悉的编辑环境（我们建议使用 IDLE，因为它与 Python 捆绑在一起并使用 Tkinter）。在后面的章节中，您需要访问互联网，以便安装 Python 软件包和 PostgreSQL 数据库。

要在 Android 设备上运行 Python 脚本，您需要在 Android 设备上安装 QPython。要使用 Kivy 库将 Python 脚本打包成 Android 的 APK，您需要安装 Kivy、Virtual Box 和 Buildozer 打包程序。同样，要在 iOS 设备上运行 Python 脚本，您需要一台 macOS 机器和一些库工具，包括 Cython。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packt.com/support](http://www.packt.com/support)并注册，以便直接通过电子邮件接收文件。

您可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在“搜索”框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用最新版本的解压缩或提取文件夹。

+   Windows 的 WinRAR/7-Zip

+   Mac 的 Zipeg/iZip/UnRarX

+   Linux 的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Python-GUI-Programming-A-Complete-Reference-Guide`](https://github.com/PacktPublishing/Python-GUI-Programming-A-Complete-Reference-Guide)[.](https://github.com/TrainingByPackt/Spring-Boot-2-Fundamentals)。如果代码有更新，将在现有的 GitHub 存储库中进行更新。

我们还有来自我们丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。请查看！

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这是一个例子：“确定每个数据字段的适当`input`小部件。”

代码块设置如下：

```py
def has_five_or_less_chars(string):
    return len(string) <= 5

    wrapped_function = root.register(has_five_or_less_chars)
    vcmd = (wrapped_function, '%P')
    five_char_input = ttk.Entry(root, validate='key', validatecommand=vcmd)
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```py
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

任何命令行输入或输出都以如下形式书写：

```py
pip install --user psycopg2-binary
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“安装后，启动 pgAdmin，并通过选择 Object | Create | Login/Group Role 来为自己创建一个新的管理员用户。”

警告或重要说明会出现在这样。提示和技巧会出现在这样。
