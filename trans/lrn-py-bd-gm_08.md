# 海龟类 - 在屏幕上绘制

不久以前，程序员，尤其是游戏程序员，在构建程序时会面临许多复杂性。难怪！那时，互联网门户的帮助还不够，包括没有堆栈溢出，更重要的是，程序员没有可以使用的通用工具；他们必须首先创建一个工具，然后在程序中使用它。他们创建的工具将处理一些游戏特定的内容（用于声音和图形的特定驱动程序）。由于资源稀缺，程序员不得不使用汇编语言创建游戏，这将是处理能力、显示、声音和控制例程的权衡。甚至在调试时也会遇到最糟糕的情况。他们需要复杂昂贵的机器来复制他们的程序，他们还需要日志记录和调试扩展。本章的主要目标是让我们熟悉使用海龟进行二维（2D）空间绘图，以及海龟的事件处理方法，并创建简单的 2D 空闲动画。

在撰写本文时，我们在游戏行业取得了巨大进步。我们已经创建了工具，使我们能够使用任何编程语言制作游戏，例如 Python 和 C（对 CPU 要求低的游戏）。由于设备驱动程序的通信，所有低级例程都被高级软件隐藏起来。Python 等高级语言是抽象的；它们提供较少的访问权限以获取低级功能。我们可以将多个东西组合在一起作为类，这些类可以从另一个类继承特性，从而消除了代码的重复。Python 提供了海龟和 Pygame 等模块，其中包含了大量用于设计游戏角色和处理用户事件的方法。在本章中，我们将学习有关海龟模块的知识。从本章开始构建的每个东西都将使用前面章节的技术，同时还会添加一些显著的特性。

本章将涵盖以下主题：

+   海龟概述

+   技术要求

+   海龟命令简介

+   海龟事件

+   使用海龟绘制形状

# 技术要求

本节将带您了解基本的 Python 图形编程模块及其工作原理。因此，您需要以下资源：

+   Python 3.5 或更高版本；参见第一章，*了解 Python - 设置 Python 和编辑器*

+   Python IDLE

+   一个文本编辑器

+   一个键盘

+   一个鼠标（笔记本电脑的触摸板无法使用）

本章的文件可以在此处找到：[`github.com/PacktPublishing/Learning-Python-by-building-games/tree/master/Chapter08`](https://github.com/PacktPublishing/Learning-Python-by-building-games/tree/master/Chapter08)

观看以下视频以查看代码的运行情况：

[`bit.ly/2pAmrCs`](http://bit.ly/2pAmrCs)

# 了解海龟模块

就像计算机的不同组件同样重要以提供更好的计算体验一样，我们也需要计算机的不同组件共同工作，以提供更好的游戏体验。计算机的显卡负责计算屏幕的视觉图像，然后在发送到显示器之前对图像信号进行模块化。输入设备如鼠标、键盘和游戏手柄需要根据程序处理用户事件。音频卡需要处理音频信号，然后将其发送到扬声器等输出设备。在游戏编程的早期阶段，程序员需要分别阅读每个设备的技术手册，并在隔离状态下编写每个设备的代码。这意味着即使是简单的游戏，它们之间的通信也需要花费一年的时间。然而，随着技术的进步，特别是驱动程序的进步，程序员免除了手动处理这些设备与操作系统之间的通信的烦恼。

尽管我们开发了一个称为驱动程序的简单程序，它作为与不同设备通信的通用接口，但不同的硬件和版本不兼容性使得程序员在开发可以在多个平台上玩的游戏时更加困难。幸运的是，我们有 Python，一种具有使程序可以跨平台的能力的语言。Turtle 是 Python 模块，提供了可以用来创建图片和图形的绘图板。据说海龟模块是上世纪 90 年代另一种流行编程语言*Logo*的姐妹模块，*Logo*有一个想象中的海龟图标和一个用于在屏幕上绘制的笔。Python 的标准库*turtle*类似于 Logo 编程语言。为了使用海龟模块，我们必须导入它。导入它更容易，因为它作为标准 Python 库打包，不需要手动安装。以下步骤解释了如何制作任何海龟应用程序：

1.  使用`import`命令导入海龟模块。如果忽略这一步，就不会有控制海龟的界面。

1.  创建一个控制海龟。这一步用于实例化海龟，以创建一个新的海龟控制器，例如，`game = turtle.Turtle()`。

1.  创建控制后，我们可以通过调用海龟模块的方法在绘图屏幕上绘制和执行多个任务。

1.  我们需要显式调用一个重要的方法，它持有游戏屏幕，即`turtle.done()`。这个方法会暂停程序。您需要手动关闭窗口以关闭应用程序。

在海龟包中，当我们运行通过调用海龟模块的方法制作的程序时，将会出现一个新窗口，带有一支笔，以及由海龟命令绘制的形状。让我们了解一些重要的海龟命令。

# 介绍海龟命令

海龟模块带有多个命令，以方法的形式独立使用。有一些方法可以使笔向前和向后移动，还有一些可以创建形状。查看下表，了解最重要的海龟命令。您可以在官方 Python 文档页面上详细了解它们：

| **方法** | **参数** | **描述** |
| --- | --- | --- |
| `Turtle()` | 无 | 创建并返回一个新的海龟对象。 |
| `forward()` | 距离 | 将海龟向前移动指定的距离。 |
| `backward()` | 距离 | 将海龟向后移动指定的距离。 |
| `right()` | 角度 | 将海龟顺时针旋转。 |
| `left()` | 角度 | 将海龟逆时针旋转。 |
| `penup()` | 无 | 抬起海龟的笔。 |
| `pendown()` | 无 | 放下海龟的笔。 |
| `up()` | 无 | 抬起海龟的笔。 |
| `down()` | None | 放下海龟的笔。 |
| `color()` | 颜色名称 | 更改海龟笔的颜色。 |
| `fillcolor()` | 颜色名称 | 更改海龟用于填充多边形的颜色。 |
| `heading()` | None | 返回当前的方向。 |
| `position()` | None | 返回当前位置。 |
| `goto()` | *x*, *y* (位置) | 将海龟移动到位置 *x*, *y*。 |
| `begin_fill()` | None | 记住填充多边形的起点。 |
| `end_fill()` | None | 关闭多边形并用当前填充颜色填充它。 |
| `dot()` | None | 在当前位置留下一个点。 |
| `stamp()` | None | 在当前位置留下一个海龟形状的印记。 |
| `shape()` | 形状名称 | 应该是 *arrow*, *classic*, *turtle,* 或 *circle*。 |

在上面的表格中，我们可以通过观察方法名称的字面意义来猜测调用这些方法的结果。例如，`forward(amount)` 方法将以指定的参数作为参数向前移动笔。所有这些方法都用于在海龟的绘图画布中绘制不同的形状。观察第一个 `>>> Turtle()` 方法。这将返回海龟的对象，必须使用该对象来调用这些方法。例如，我们将编写一个程序，该程序将在屏幕上绘制一条线。以下是此示例的代码：

```py
import turtle

pacman = turtle.Turtle()
pacman.forward(100)

turtle.done()
```

通过运行上面的代码，我们可以观察到以下输出：

![](img/6b556b6f-26e0-4e80-8b39-5a7fd539ebe5.png)

连同 Python shell 一起，新屏幕应该像之前的那个一样弹出，这代表了海龟绘图板。最初，附着在虚拟海龟上的笔将驻留在绘图板的中心。海龟对象的任何方法调用都必须操纵笔的移动。上面的代码可以通过以下步骤来解释：

1.  首先，我们必须导入 turtle，这是一个第一步，将确保海龟类中的所有命令对我们可用。

1.  第二步是创建一个海龟控制器，我们称之为吃豆人。

1.  然后，我们从吃豆人面对的点向前移动 100 像素。最初，*吃豆人* 海龟控制器是朝右的；因此，笔从中心向右移动 100 像素，形成了一条直线。

1.  最后，`turtle.done()` 将暂停海龟绘图板屏幕，以便我们可以清楚地观察输出。为了关闭海龟屏幕，我们必须手动关闭 Python shell 或海龟图形屏幕。

我们刚刚学会了如何创建一条直线，但是这些线看起来很无聊，对程序没有任何美感。现在是学习如何使用另一个方法的时候了，这个方法将转动笔到另一个方向。例如，我们可能想要将笔的方向从最初的方向改变到另一个方向：

```py
import turtle

pacman = turtle.Turtle()

pacman.forward(50)
pacman.right(90)     

pacman.forward(50)
pacman.right(90)

pacman.forward(50)
pacman.right(90)

pacman.forward(50)
pacman.right(90)

turtle.done()
```

我们已经熟悉了`forward`方法，现在我们引入了`right()`方法。如果你看一下之前的方法表，你会发现`right`方法和角度作为参数传递了进去。因此，这个方法将执行一些旋转，并伴随着传递进去的角度。由于我们传递了 90 度，这个方法将进行一个 90 度的顺时针旋转。如果你想要将笔逆时针旋转，我们需要调用 left 方法并指定旋转的角度。在前面的程序中，我们将它旋转了 90 度。所有角度都是 90 度的几何形状要么是正方形，要么是长方形。然而，我们知道`forward`方法会产生一条直线，这与几何形状的边是一样的。由`forward`方法创建的边的长度是相等的，为 50，这作为`forward`方法的参数传递进去。有了这些证据，我们可以肯定地期望在乌龟画板上画出一个正方形形状。让我们运行前面的代码来观察输出。正如预期的那样，画出了正方形形状：

![](img/0ba32ee6-1a60-4f2c-9cdb-04c4e19b47da.png)

仔细看一下前面的代码；你看到了一些代码的重复吗？显然，`forward`和`left`方法的调用被多次执行，这最终违反了 DRY 原则。这种顿悟并非没有练习 Python 范式而来。因此，我们可以说练习是区分好坏程序员的关键。现在，回想一下我们需要什么来消除代码的冗余；我们应该使用循环或函数。我们将在这里使用一个循环：

```py
import turtle

pacman = turtle.Turtle()
for i in range(4):
    pacman.forward(50)
    pacman.right(90)

turtle.done()
```

我猜我们在阅读和理解这段代码时不会遇到任何问题。正如我们在第三章中提到的，*流程控制 - 为你的游戏构建决策者*，我们可以使用一系列函数创建迭代级别。由于我们需要运行这些方法四次，我们使用了 range 函数创建了四次迭代。任何需要重复的内容都会在 for 循环的范围内缩进四个块。

在这个例子中需要注意的一点是，我们有多个处理画板上笔的移动的方法。到目前为止我们学到的两个乌龟命令是`forward(amount)`，它将乌龟向它所面对的方向前进一定距离，以及`right(degree)`，它使乌龟顺时针旋转指定的角度。请注意，`right`和`left`命令不会在屏幕上写任何东西；它们只用于旋转。

根据我们迄今为止学到的一切模式，我们可以预测`backward`方法将会将笔从原来的方向向后移动指定的距离。我建议你尝试稍微修改前面的代码 - 通过使用`backward`重构`forward`方法，通过使用`left`重构`right` - 并相应地观察结果。我想在这里花点时间总结这个话题，而不涉及其他函数，因为我们将在接下来的章节中制作游戏时逐个学习它们。我们将制作多个游戏，比如蛇游戏、乒乓球游戏和使用乌龟模块的 Flappy Bird。现在，我们将探索如何连接输入设备，比如鼠标和键盘，到我们的游戏中，以便玩家可以与乌龟环境进行交互。

# 探索乌龟事件

正如我们在前面的章节中提到的，处理用户事件是创建任何游戏的主要构建块之一。事件代表了在游戏过程中任何时候需要执行的动作。你是否曾经想过程序是如何在低层次处理事件的？当用户使用键盘或鼠标执行任何事件时，该请求被存储在一个类似队列的结构中。队列结构很重要，因为处理这些事件的顺序必须是先来先服务的。然后，根据用户操作的行为，程序处理事件。这两个任务——渲染和动作处理——由程序独立执行。例如，在反恐精英游戏中，用户可以从枪中射击，即使周围没有敌人。这里，事件是用户按键开枪，渲染任务是在玩家周围生成敌人。除非我们编写程序来执行这两个任务，否则这两个任务不会独立执行。在本节中，我们将学习如何将用户动作作为输入，并相应处理。处理用户动作意味着服务存储在队列结构中的动作。

大多数事件都是基于鼠标或键盘的使用，但有些事件必须由程序自动预测并相应处理，比如“ontimer(fun, time)”方法。这个方法接受两个参数：函数和毫秒时间。这个方法设置一个定时器，在“time”毫秒后调用“fun”函数。让我们做一个简单的程序来理解这一点：

```py
import turtle
star = turtle.Turtle()

exit = False
def main():
    if not exit:
        for i in range(100):
 star.forward(i)
 star.right(144)
main()
turtle.mainloop()
```

代码的最后一行（`turtle.mainloop()`）只是执行了在循环中执行的相同操作。直到用户明确退出窗口屏幕，对“main”函数的调用才会终止。当程序有一个用于监听传入连接的 while 循环时，它的重要性就会显现出来，但我们不希望计算机一直专注于这一情况：

```py
def draw_objects():
    #statements
    draw_objects() #may be you want to call it within the time interval 
                    of 100ms

draw_objects()
turtle.mainloop()
```

前面的代码与 while 循环的工作方式完全相同，但现在 Python 解析器不再专门执行一个任务。相反，每 100 毫秒，“draw_objects()”任务将被执行，而剩下的 99.99 毫秒，Python 解析器可以自由执行任何其他任务。

有趣的是，前面的代码代表了任何 turtle 程序的正确结果。虽然调用不同的函数会在屏幕上显示不同的字符，但使用 turtle 的主要目的是将游戏角色渲染到屏幕上。让我们将前面的代码分解成以下几点：

+   前几步代表着导入 turtle 并创建一个 turtle 控制器，这将允许我们通过它调用所有的“turtle”方法。

+   我们创建了一个“main”函数，在其中，我们有一些代码来创建一个星形图案。迭代次数是 100 次，这意味着我们将在输出屏幕上打印 100 颗星星，但请记住，它们会很接近。

在屏幕上正确渲染字符的最佳方法是使用“ontimer”方法。让我们用“ontimer”方法修改相同的程序。让我们看看如何在程序中使用它：

```py
import turtle
star = turtle.Turtle()

exit = False
def main():
    if not exit:
            star.forward(50)
            star.right(144)
    turtle.ontimer(main,500)
main()
```

与以前不同，前面的程序不会打印多个星星；相反，它打印一个单一的星星。然而，“ontimer”方法消除了调用 for 循环的开销，因为它设置了定时器来一遍又一遍地调用相同的函数。在这个程序中，我们传递了“main”函数和 500 作为参数，这意味着“main”函数应该在每 500 毫秒调用一次。运行前面的程序将产生以下输出：

![](img/56a5bc78-7ded-4b06-95fb-c27435241fa5.png)

现在是时候学习如何处理键盘和鼠标事件了。和往常一样，已经定义了用于处理键盘事件的方法和用于处理鼠标事件的方法。但是，在处理用户事件之前，乌龟必须启动一个监听器，它会持续保持清醒状态以监听任何事件。使用`listen`方法创建这样一个监听器控制器，即`>>> turtle.listen()`。以下表格描述了用于处理键盘事件的方法：

| **方法名称** | **参数** | **描述** |
| `turtle.onkeypress(function, key = None)` | **Function**：没有参数或`None`的函数。**Key**：以字符串或符号形式的键，例如，`q`或`space`。 | 用于将函数绑定到键盘上按下的任何键事件。如果未指定键，则任何键都可以使用。 |
| `turtle.onkeyrelease(function, key)` | **Function**：没有参数或`None`的函数。**Key**：以字符串形式的键，如`a`，或符号，如`enter`。 | 用于将函数绑定到键释放事件。如果函数为`None`，则解除事件的绑定。 |

让我们编写一个简单的程序，以便掌握使用这些处理键盘操作的方法的思想：

```py
import turtle
star = turtle.Turtle()
def main():
    for i in range(30):
        star.forward(100)
 star.right(144)
turtle.onkeypress(main,"space")
turtle.listen()
turtle.mainloop()
```

让我们运行程序并观察输出。按下*F5*后，你会看到两个屏幕，其中一个会有乌龟图形板和笔位于其中心。现在，按下键盘上的*Spacebar*键。一旦按下，它就开始在屏幕上绘制一个星星。

在`main`函数内部，我们添加了一些代码来制作一个星星。然而，正如你所看到的，`main`函数并没有被显式调用，就像我们通常调用函数一样；相反，它是使用`onkeypress`方法调用的。这个方法将键绑定到函数，每当按键时，函数就会自动调用。如果从前面的代码中删除最后一行，监听控制器就不会起作用。`listen`方法用于创建一个控制器，不断监听这些类型的操作。

以类似的方式，我们可以调用`onkeyrelease`方法。在前面的代码中用`onkeyrelease`替换`onkeypress`，并观察输出。输出将是相同的。`onkeyrelease`方法用于将函数绑定到按键释放事件。

同样，处理鼠标事件的方式也并不太不同——它们也是通过方法调用来处理的。以下表格描述了可以用来处理鼠标事件的方法：

| **方法** | **参数** | **描述** |
| `onclick(function, button = 1, add = None)` | **Function**：使用两个参数（*x, y*）调用一个函数，表示鼠标或指针点击位置的坐标。**Button**：表示鼠标按钮，`默认 = 1`，表示左键。**Add**：用于添加多个绑定。如果传递`True`，将添加新的绑定，否则将保持当前绑定。 | 将函数绑定到鼠标点击事件。如果用户点击乌龟画布的任何位置，将使用点击位置的坐标调用函数。 |
| `onrelease(function, button = 1, add = None)` | **Function**：使用两个参数（*x, y*）调用一个函数，表示乌龟绘图板上点击位置的坐标。**Button**：`默认 = 1`表示使用左鼠标按钮。用于添加鼠标按钮的数字。**Add**：根据`True`或`False`的值，决定是否添加新的绑定。 | 将函数绑定到鼠标按钮释放事件。 |
| `ondrag(function, button = 1, add = None)` | **Function**：带有两个参数的函数，表示点击点的坐标进入游戏屏幕。**Button**：添加一个数字以指示鼠标按钮监听器。 | 将函数绑定到当前海龟控制器上的鼠标移动事件。如果函数为`None`，则将删除当前绑定。 |

让我们制作一个简单的程序来理解如何使用前面的方法处理鼠标事件：

```py
import turtle
pacman = turtle.Turtle()
def move(x,y):
    pacman.forward(180)
    print(x,y)

turtle.onclick(move) #calling move method
#turtle.onclick(None) #to remove event-binding   
```

您可以看到`onclick`方法只调用了移动函数，然后移动方法使用代表点击点的*x*和*y*坐标在画布上。运行上述程序在屏幕上不会绘制任何线条，直到您点击绘图画布。当您点击屏幕上的任何点时，您将在 Python shell 中看到其坐标，并且直线将出现在画布上。我们将在接下来的章节中介绍剩余的`turtle`方法，以及如何制作一些小游戏。在那之前，我们将尝试使用`turtle`模块和迄今为止学到的 Python 设计模式来制作一些形状。

# 使用海龟绘制形状

制作形状的过程对人类来说可能看起来是一项乏味和繁琐的任务，但对计算机来说并非如此。想象一下在考虑角度和边的情况下制作具有精确几何测量的六边形。这个过程本身就会让我们大多数人感到不知所措。另一方面，计算机被认为是勤奋工作的；我们可以向它抛出尽可能多的任务，它会优雅地执行它们。

正如我们之前提到的，绘制任何形状时两个关键信息是每条边的角度和长度。我们可以创建变量来存储它们，以便在程序中需要时引用它们。对于任何形状，边的数量都会不同。例如，三角形有三条边，而六边形有六条边。我们需要在程序中明确指定边的数量。在本节中，我们将制作两种形状，一个六边形和一个星形，还加上一些颜色。本节的主要目的是帮助您了解编程范式是如何使用的，以及特定模块，以制作吸引人的游戏。

以下步骤列表描述了为了逐个创建两个形状所需的路线图。我们将首先创建的形状是一个六边形：一个有六条边的形状，具有自定义长度。之后，我们将再次制作星形图案，但这次我们将为其添加颜色属性：

+   **六边形**：我们将通过定义特定变量来创建这个形状，比如边的数量、内角和边的长度。之后，我们将使用 for 循环创建六次迭代，因为我们必须调用线渲染方法六次（因为六边形有六条边）。我们将使用`forward`方法绘制一条直线，使用`right`方法将海龟顺时针旋转特定角度：

```py
      import turtle 

      hexagon = turtle.Turtle()

      num_of_sides = 6
      length_of_sides = 70
      angle = 360.0 / num_of_sides 

      for i in range(num_of_sides):
          hexagon.forward(length_of_sides)
          hexagon.right(angle)

      turtle.done()
```

+   您可以看到使用`turtle`模块在画布上绘制形状是多么方便。我们已经熟悉了这些方法以及使用循环来消除多行代码的重复；因此，理解我们在这里编写的代码不会很难。

+   **星形**：使用 Turtle 制作星形比使用任何其他模块更容易。我们已经使用了`turtle`的两种方法来制作它，即`forward`和`left`。但在本节中，我们将使用`turtle`模块提供的`color`方法为星形上色。我们将首先定义颜色调色板，即不同的颜色名称，并且我们将调用`begin_fill`和`begin_end`方法，这将为形状添加颜色。以下表格显示了可以用于给海龟着色的三种方法：

| **方法** | **参数** | **描述** |
| `color(*args)` | Args 代表颜色的名称。当前颜色用于使用`forward`或`backward`方法绘制线条。颜色名称可以作为`单个值：color(“blue”)`，`双值：color(“black”,”green”)`或`rgb`浮点值给出。 | 用于改变乌龟笔的颜色。 |
| `begin_fill()` | 无 | 这个方法将记住填充多边形的起始点。 |
| `end_fill()` | 无 | 它将关闭在乌龟画布中绘制的形状并用当前填充颜色填充它。 |

例如，我们将编写一个程序，使用这些方法来给星形图案上色。我们将使用红色和黄色的颜色组合来使星星更具吸引力。我们一直在使用“import turtle”命令使“turtle”方法可供程序使用。与其这样做，我们可以使用“from turtle import *”命令从 turtle 中导入所有内容。现在，我们可以直接调用“turtle”方法，即“forward(100)”，而不是使用“>>> turtle.forward(100)”来调用它。让我们编写一个程序来创建这样一个星形图案：

```py
from turtle import *

color('red', 'yellow')
begin_fill()
while True:
    forward(200)
    left(170)
    if abs(pos()) < 1:
        break
end_fill()
done()
```

我非常喜欢乌龟与 Python 一起工作的方式。能够将每个函数绑定到 Python 的编程范式使得“turtle”模块的使用效果很好。在前面的代码中，我们可能不会对第一行代码感到困惑，它只是从“turtle”模块中导入了所有内容——每个属性和成员。我们使用“color”方法制作了红色和黄色的调色板。在主循环中，我们将遇到两种方法，这些方法我们从本章开始就一直在使用。此外，我们添加了一个条件来指示乌龟笔的停止点。`abs()`方法用于返回数字的绝对值，即`>>> abs(-4)`，得到 4。在`abs()`函数内部，我们调用了“turtle”模块的“pos()”方法，它将返回乌龟的位置作为一个两元素列表。我们检查了当前位置，如果小于 1，例如 0，那么它必须代表中心位置，因为(0,0)代表中心位置。如果我们在迭代后遇到中心位置，那么这意味着我们可以终止程序，因为在这一点上，我们必须已经画了一个星星。如果我们继续，乌龟笔将在相同的位置上画另一个星星，从而覆盖旧的星星。

因此，为了防止这种连续迭代，我们添加了一个条件行：`if abs(pos()) < 1`。

执行上述程序会产生以下输出。在这里你必须记住的一件事是，从调色板开始，我们使用红色笔画星星，完成后，我们使用黄色填充星星形状的内部：

![](img/d9d18f00-4b2e-4228-9cad-dc4770931526.png)

现在您已经了解了使用“turtle”方法创建形状并对其上色的方法，我们将在此结束本章。我们将在接下来的章节中使用本章学到的概念，如创建图案和处理用户事件，制作简单的迷你游戏，比如 Snake、Pong 和 Flappy Bird。

# 总结

Python 的*turtle*模块是构建 2D 迷你游戏的强大平台。它包含各种方法，以便简化游戏角色的设计过程。我们在本章中编写了许多程序，并处理了用户事件。我们通过介绍“turtle”模块的关键特性开始了本章，并为可以使用 Python“turtle”模块制作的任何游戏构建了一个通用原型。本章教会了我们如何使用“turtle”模块来制作 2D 画布动画。除了为游戏角色添加动画，我们还学会了如何通过处理用户事件创建游戏界面和用户控制器之间的通信接口。

完成本章后，你将能够使用“turtle”模块创建简单的 2D 游戏。你还将能够处理鼠标和键盘提供的用户操作，这使我们能够制作用户交互式游戏。现在你已经学会了如何使用 2D Turtle 画布创建简单的动画，你可以创建任何几何形状；在进入下一章之前再尝试几个。

本章我们没有涉及任何游戏，因为要使用“turtle”模块创建游戏，我们首先需要探索向量——创建向量、存储向量、找到向量的大小、向量相加、否定、对角线移动等等。我们将在下一章中涵盖所有这些概念。

向量的主题无疑是任何游戏开发者工具包中最基本的主题。向量是代表屏幕上出现的游戏角色的大小和方向的数学术语。大小表示角色所在点的当前坐标的模，而方向表示游戏角色移动的方向。现在是你玩弄“turtle”模块并掌握处理用户事件以及构建吸引人的形状和角色的完美时机。