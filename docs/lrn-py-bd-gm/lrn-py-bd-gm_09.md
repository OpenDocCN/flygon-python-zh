# 第九章：数据模型实现

游戏是一种通过互动来模拟或至少模拟真实世界环境的媒介，玩家通过动作和移动来控制游戏角色。我们知道，玩家可以用键盘、鼠标或操纵杆等输入设备与游戏进行互动的方式有很多种。为了将这些输入信号转化为有意义的信息，我们需要用相应的动作来处理这些信号。在大多数游戏中，我们使用键盘按键来移动游戏角色，但在内部，这些信号是由称为向量的数学对象处理的。这对于任何游戏来说都非常重要，无论图形看起来如何，因为它导致玩家产生动作并用适当的反应来处理它们。

在本章中，我们将介绍二维向量——操纵游戏角色位置的方法。向量坐标（*x*，*y*）的变化代表了游戏玩家指定的移动。对于任何编程初学者来说，本章将是改变生活的，因为它将教会我们如何使用数学概念，如加法、减法、乘法、旋转和反射，以及数据模型实现这样的编程范式。本章的最终目标是让您熟悉使用 Python 进行运算符重载的概念，使用 Python 内置方法来操纵向量位置，以及实现数据模型或魔术函数。

本章将涵盖以下主题：

+   运算符重载概述

+   技术要求

+   处理二维向量

+   向量运动的数据模型

# 技术要求

本章将带领我们体验 Python 简单而强大的运算符重载概念。因此，您需要具备以下工具：

+   Python 3.5 或更新版本

+   Python IDLE（Python 内置的 IDE）

+   文本编辑器

+   网络浏览器

本章的文件可以在这里找到：[`github.com/PacktPublishing/Learning-Python-by-building-games/tree/master/Chapter09`](https://github.com/PacktPublishing/Learning-Python-by-building-games/tree/master/Chapter09)

观看以下视频以查看代码的运行情况：

[`bit.ly/2psS6pd`](http://bit.ly/2psS6pd)

# 理解运算符重载

这是一个新概念，对于初学者来说可能会有些模糊，但有必要了解这一知识。在编程术语中，用编程语言定义的一切都有特定的用途。例如，我们不能使用`sum()`方法来找到元素之间的差异。我们可以扩展任何操作的含义超出其正常用法或预定义的操作用法。以加法（+）运算符为例；这个运算符可以用来添加简单的整数，连接两个独立的字符串，甚至合并两个列表。这是因为加法运算符在不同的类中被重载，也就是说，在字符串和整数类中定义了不同的实现。这就是运算符重载的威力。

还需要记住的一点是，相同的函数或内置运算符对于多个类的对象具有不同的行为，如下例所示：

```py
>>> 6 + 6
12
>>> "Python" + " is " + "best"
'Python is best'
>>> [1,2,3] + [4,5]
[1,2,3,4,5]
```

有几种方法支持运算符重载；这些被称为数据模型，有时也被称为魔术方法。之所以这样称呼，是因为这些特殊方法扩展了方法的功能，从而为我们的类增添了魔力。这些数据模型不应该由我们调用；而是在类内部自动发生。例如，当我们使用`+`运算符执行加法操作时，Python 解析器内部调用`__add__()`方法。Python 的不同内置类，如`str`、`int`、`list`等，都有不同的内部定义的魔术函数。我们可以使用`dir`函数打印专门用于特定类的魔术函数列表。例如，以下列表指示了`str`类中定义的几种方法和属性：

```py
>>> dir(str)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

如前面的`str`类的方法和属性列表所示，我们可以观察到几个以双下划线开头和结尾的方法。例如，`__add__()`方法用于使用`+`运算符连接两个字符串：

```py
>>> first_name = "Ross"
>>> first_name.__add__(" Geller")
'Ross Geller'
```

在上面的示例中，我们可以看到`__add__()`函数的工作方式与`+`类似。这些数据模型旨在用于扩展具有重载行为的预定义含义。根据数学规范，我们通常使用`+`、`-`、`/`和`*`等运算符与数字对象一起使用。然而，通过重载技术，我们可以使用相同的运算符来处理多个对象，如字符串和列表。我们可以得出结论，加法运算符（`+`）被重载。同样，Python 为不同的运算符定义了不同的数据模型，即`__sub__()`用于`-`运算符，`__mul__()`用于`*`运算符，`__div__()`用于`/`运算符。

现在我们已经学会了如何在最基本的形式中使用数据模型来实现 add 函数，我们将实现一些自定义类的示例。

# 在自定义类中使用数据模型

现在我们知道如何使用`__add__()`魔术函数与整数和字符串等各种数据类型一起使用，让我们观察它如何在自定义（用户定义的）Python 类中使用。我们将考虑以下示例来说明数据模型在我们的自定义类中的用法：

```py
class Base:
    def __init__(self, first):
        self.first = first

    def add(self, other):
        print(self.first + other)
```

我们将使用以下代码创建前面类的对象。此代码在 Python shell 中执行：

```py
>>> obj1 = Base(1)
>>> obj2 = Base(2)
>>> obj1.add(obj2)
TypeError: unsupported operand type(s) for +: 'int' and 'Base'
```

正如预期的那样，我们得到一个错误，说不支持不同类型的操作数，这意味着`+`运算符对于添加自定义类的对象是不起作用的。如前所述，为了解决这类问题，我们可以使用运算符重载。我们可以在我们的类中显式定义这些特殊方法，以使对象与内置方法和运算符兼容。例如，在加法操作的情况下，我们必须在类内部显式定义`__add__()`方法，看起来像这样：

```py
class Base:
    def __init__(self, first):
        self.first = first

    def __add__(self, other): #operator '+' is overloaded 
 print(self.first + other.first)    
```

让我们通过创建`Base`类的不同对象来检查这是否有效：

```py
>>> obj1 = Base(1)
>>> obj2 = Base(2)
>>> obj1.__add__(obj2)
3
#for strings as add method is defined internally inside str class
>>> obj3 = Base("Hello ")
>>> obj4 = Base("World")
>>> obj3.__add__(obj4)
'Hello World'
```

因此，魔术函数，或者`__add__()`数据模型被重写，成功地在两个整数和两个字符串之间执行了加法操作。我们也可以检查其他数据对象，如列表和元组。现在，我们可以清楚地预测模式；如果我们想要重载任何数学运算符并在我们的自定义类中以不同的方式实现它，我们必须在我们的类中定义数据模型。希望你明白了！现在，我们可以预测`__mul__()`模式，以便我们可以在不同对象之间执行乘法，`__sub__()`执行减法，等等。

在实际学习使用这些魔术函数的重要性之前，让我们先观察 Python 中另一个强大但不太常用的魔术方法，即`__new__()`数据模型。你可以轻松观察这些方法的工作方式；只需删除方法名称周围的下划线和括号，你就会得到`new`关键字。如果你有来自 Java 和 C#等高级语言的编程背景，你可能已经理解我的观点。对于那些对`new`关键字的概念还不熟悉的人，这个操作符用于创建类的实例。例如，在 Python 中，我们有`object = class_name()`，而在 Java 中，我们有`object = new class_name()`。

因此，`__new__()`魔术方法是在创建类的对象时调用的第一个方法，甚至在调用`__init__()`构造函数之前就调用了它，并且是隐式调用的。`__new__()`方法负责创建新对象，并返回使用构造函数的`__init__()`方法初始化的对象。你还记得，在面向对象的章节中，我们将`__init__()`方法称为特殊方法，实际上是一个魔术方法。让我们考虑以下示例来了解`__new__()`魔术方法：

```py
class Base:
    def __new__(cls):
        print("This is __new__() magic method")
        obj = object.__new__(cls)
        return obj
    def __init__(self):
        print("This is __init__() magic method")
        self.info = "I love Python"
```

以下代码在 Python shell 中执行。我们正在创建一个`Base`类的对象，并观察到在`init`方法之前调用了`new`方法：

```py
>>> obj = Base()
This is __new__() magic method
This is __init__() magic method
```

请注意，在上述代码中，我们在定义`new`魔术方法时将`cls`作为参数传递，并在定义`init`构造函数时将`self`变量作为参数传递。`cls`和`self`这两个变量之间的区别在 PEP 8 中有定义，它定义了 Python 代码的样式指南。这种编码风格并不是强制性的，但根据 PEP 8，我们应该始终做到以下几点：

+   始终将`self`用作实例方法的第一个参数。

+   始终将`cls`用作类方法的第一个参数。

我认为我们现在已经足够能够预测任何内置函数的工作内部。让我们以`len()`方法为例。如果 Python 中有任何内置的`fun()`函数，它对应于`__fun__()`。Python 解析器会进行内部调用，如`object.__fun__()`，其中对象是类的实例。考虑到这个类比，对于`len()`函数，Python 解析器将其调用解释为`object.__len__()`，并返回对象的长度。我们已经看到了它的内部工作方式；然而，由于我们想要覆盖的主要主题是如何覆盖它，让我们在我们自定义的类中定义这个魔术方法（类似于前面的例子，我们使用`add`魔术函数来添加类的对象）。在`__len__()`的情况下，考虑以下示例：

```py
>>> info = "I love Python"
>>> len(info)
13
>>> info.__len__()
13
```

因此，当我们在自己的类中定义这样的魔术方法或数据模型时，我们覆盖了 Python 原始定义的函数的行为；因此，我们不再调用原始方法。当你用新方法覆盖原始方法时，我们称之为方法覆盖。到目前为止，我们一直在学习数据模型及其在我们自己的类中的使用方式。现在，让我们学习为什么它们在游戏编程中是必不可少的。我们将在下一节中通过探索向量来做到这一点。

# 处理二维向量

在实际探索向量之前，让我们从运动的基本概述开始，以及如何使角色在直线上移动。要移动任何对象或图像，我们必须对每一帧进行微小的固定量的改变。运动必须对每一帧都是固定的，以使其对称。要使一个对象在水平方向上移动，我们对*x*位置进行固定量的加法，要使其在垂直方向上移动，我们对*y*位置加上相同的量。因此，2D 游戏中的运动可以表示为（*x*，*y*）。让我们考虑以下例子，以说明如何使用这些坐标来在游戏环境中绘制任何形状：

```py
def line(a, b, x, y):
    "Draw line from `(a, b)` to `(x, y)`."
    import turtle
    turtle.up()
    turtle.goto(a, b)
    turtle.down()
    turtle.goto(x, y)
```

我们使用了在上一章中使用的`turtle`模块，用于使用（a，b）和（x，y）位置绘制线条。`goto()`方法用于将笔移动到传递的位置。这些坐标——（x，y）或（a，b）——清楚地显示了知道位置以创建游戏角色的重要性（我们使用线条作为任何游戏角色的比喻）。

我们可以认为直线运动的使用非常有用，但从不同的角度来看，一个只支持垂直或水平运动的游戏可能会显得乏味和无聊。例如，在吃豆人游戏中，玩家只能在垂直或水平方向上移动，这可能是合适的，但在赛车游戏中，用户可以朝任何方向移动，这种运动就不适用了。我们必须能够通过调整每一帧的*x*和*y*位置来朝任何方向移动。我们将使用相同的*x*和*y*位置来生成直线和对角线运动：一个表示*x*和*y*位置速度的比率。表示`(x, y)`的形式被称为向量，但更重要的是，向量表示方向，而标量不表示。我们将在下一小节中更详细地探讨向量。

# 探索向量

正如数学格言所说：

“向量是指任何具有大小和方向的量，特别用于确定空间中一个点相对于另一个点的位置。”

我们完全同意。这个概念源自数学，对于任何游戏程序员来说都是最知名的话题，无论是天真还是老练。向量是任何对象位置的正确表示，附加了方向的关键信息。向量与直线运动的形式相似，以*x*和*y*坐标（2D）的形式表示，但它们不仅仅限于提供大小的信息；它们有一个特定的目的。例如，向量（4，5）表示下一个位置，其中 4 被添加到当前位置的*x*坐标，5 被添加到当前位置的*y*坐标；类似于这样——（0 + 4，0 + 5）——其中（0，0）是原点或中心位置。让我们用以下例子形象地来研究向量：

![](img/9d2fb736-745b-402b-9479-7af3f02d1681.png)

在前面的图表中，向量（4,5）具有大小和方向。绿线表示大小，橙线表示方向。因此，一个向量如果没有前面的方向信息就是不完整的。让我们看另一个简单的例子来进一步澄清这一点：

![](img/7047fb35-abc6-4eef-99f4-a175f1310dcc.png)

前面的图表已经说明了一切。向量 AB 是目标位置与初始位置的*x*和*y*位置的减法。假设一个吃豆人在位置（30，20），他必须到达目标位置（50，45）。向量 AB 是关键信息，表明吃豆人在*x*方向上还需要移动 20 个单位，在*y*方向上还需要移动 25 个单位。

众所周知，Python 没有内置的*向量*数据结构。如果你认为有的话，快速在互联网上搜索一下，你会得到基本的概念。然而，在前面的章节中，我们并没有涵盖向量作为内置数据结构。虽然我们没有向量作为内置数据类型，但我们可以自己制作一个。正如我们所知，向量包括两个不同的位置（*x*，*y*），我们的主要目标是使用其他内置数据结构来制作它们。例如，我们可以使用列表来制作向量，但使用索引来表示每个点，如`[0]`和`[1]`，会增加不必要的开销。元组也是如此。创建向量的最佳方式可能是制作我们自己的向量类。这样做，我们可以引用点为*x*和*y*，而不是索引。此外，使用数据模型与向量可以获得最佳的利用。我们可以在向量类中使用`__add__()`，`__mul__()`和许多其他魔术函数，这将为游戏角色引入运动。例如，我们将创建一个简单的向量类，并使用`__str__()`方法，以及一个构造函数，它将提供向量位置的适当表示：

```py
class Vector(object):
    def __init__(self, x = 0, y = 0):
        self.x = x
        self.y = y
    def __str__(self):
        return "(%s, %s)"%(self.x, self.y)
```

在前面的程序中，我们创建了一个`Vector`类，并在其中定义了两个成员：一个是构造函数，另一个是魔术方法。现在，当我们创建这个类的任何对象时，比如`> pos = Vector(10,40)`，`init()`方法将执行初始化，这样我们就可以引用向量的每个分量，如`>>> pos.x`和`>>> pos.y`。`__str__()`方法是魔术方法，它被用作覆盖方法，并且在我们的`Vector`类中有一个自定义定义，用作向量分量的表示形式，即*x*和*y*位置。让我们通过运行以下代码并创建一个`Vector`类对象来看看它是如何工作的：

```py
>>> pos = Vector(10, 40)
>>> pos.__str__()
'(10, 40)'
```

除了`__str__()`方法之外，我们还有一堆适用于操作向量的魔术函数。我们可以使用`__add__()`执行向量之间的加法，`__sub__()`执行减法，`__neg__()`执行否定等。我们将在下一节学习这些数据模型以及使用它们修改向量的方法。

# 用于向量运动建模

正如我们所知，向量是构成大小和方向的量。当根据用户的行动确定游戏角色的下一个位置时，这两个信息非常关键。例如，游戏角色 Steve（一个 Minecraft 角色）可以使用向量来确定他必须使用大小（AB）和方向（→AB）来跟踪他的目标。虽然我们可以逐个更改这些信息源，但我们主要关注大小，因为大小负责在 2D 游戏中提供运动。在本节中，我们将揭示教我们如何添加和减去向量，甚至执行乘法和除法的技术。这些类型的操作将作为逻辑添加到游戏中，以及用户事件，因此每当用户在键盘上按下任何键时，它都会被特定事件处理。在进行这种数学操作时可以使用的技术如下：

+   对已知分量的向量进行操作（减法/加法）

+   通过查找分量执行操作，或者简单地使用头/尾方法

让我们学习如何使用这些技术，跳到下一节，在那里我们将使用魔术函数或数据模型执行向量操作。

# 向量加法

与数值加法类似，我们可以使用“__add__（）”数据模型重载`+`运算符，它将添加两个不同的向量并将其效果组合以产生一个新的单一向量。使用此方法，我们可以使游戏角色进行对角线运动。我们需要两个向量来执行加法；第一个将是游戏角色的当前位置，下一个将是用户在键盘上按下任何键时需要添加的向量的每个分量的预定义固定量。以下图示了向量加法的详细过程：

！[](Images/eadbff9a-5933-446d-9314-fa85759bc534.png)

当您有一个由元组或列表表示的向量时，永远不要使用`+`运算符执行向量的加法操作。[1,2] + [3,4]不会像这样添加单个数字：[4,6]。相反，它将两个列表连接成一个，如下所示：[1,2,3,4]。

以下代码使用“__iadd__（）”魔术函数来添加两个向量。`iadd`和`add`方法的工作方式类似，但它们之间的主要区别是“__iadd__（）”将其结果存储在内存位置中，而“__add__（）”不会。您可以使用其中任何一个来编写以下代码：

```py
def __iadd__(self, other):
    if isinstance(other, Vector):
        self.x += other.x
        self.y += other.y
    else:
        self.x += other
        self.y += other
    return "(%s, %s)"%(self.x, self.y)
```

确保前面的代码包含在先前创建的`Vector`类中。 “__iadd__（）”方法接受参数* other *，表示需要添加到其上调用的向量的第二个向量。在魔术函数内部，我们已经制作了条件语句，以检查传递的* other *向量是否是`Vector`类的类型。如果是，我们将第一个向量的匹配分量与第二个向量相加，即`first.x`到`second.x`和`first.y`到`second.y`，其中 first 和 second 是向量。让我们创建`Vector`类的实例并检查向量加法的输出：

```py
>>> a1 = Vector(10,20)
>>> a2 = Vector(30,40)
>>> a1.__iadd__(a2)
'(40, 60)'
```

现在我们已经成功使用魔术方法来实现向量加法，是时候学习更多的方法来实现向量减法和向量否定了。

# 向量减法

就像向量的加法意味着游戏角色的前进运动一样，向量的减法暗示着与当前面向相反的方向。我们可以使用“__sub__（）”或“__isub__（）”来实现向量减法。我们通常更喜欢`isub`，因为它在返回结果之前存储结果，并且可以完美地用于克隆向量对象，以便我们可以在重复对象中执行不同的操作，而不会损害原始对象。向量减法与加法非常相似；而不是添加向量的每个分量，我们只是要减去它们。这种运动在游戏中非常有用，比如吃豆人，用户必须在不干扰游戏过程的情况下突然改变方向。让我们在`Vector`类中编写以下代码，以执行向量减法：

```py
def __isub__(self, other):
    if isinstance(other, Vector):
            self.x -= other.x
            self.y -= other.y
        else:
            self.x -= other
            self.y -= other
        return "(%s, %s)"%(self.x, self.y)
```

让我们在 Python shell 中运行上述代码，以观察向量减法的结果：

```py
>>> a1 = Vector(10,20)
>>> a2 = Vector(30,40)
>>> a1.__isub__(a2)
'(-20, -20)'
```

# 向量乘法和除法

乘法和除法等操作将使向量变大和变小。由于乘法而产生的运动变化可能是线性的，当向量乘以任何标量时。例如，当我们将任何向量乘以二时，其大小将是以前的两倍，但方向将保持不变。同样，当我们用负数乘以相同的向量，比如-2 时，它的方向将与最初的方向相反。乘法操作通常用于缩放向量。我们可以按以下方式乘以和除以两个向量：

```py
def __imul__(self, other):
        if isinstance(other, Vector):
            self.x *= other.x
            self.y *= other.y
        else:
            self.x *= other
            self.y *= other
        return "(%s, %s)"%(self.x, self.y)

def __itruediv__(self, other):
        if isinstance(other, Vector):
            self.x /= other.x
            self.y /= other.y
        else:
            self.x /= other
            self.y /= other
        return "(%s, %s)"%(self.x, self.y)
```

与向量乘法和除法类似，我们可以使用标量数量进行缩放过程。我们将传递一个数字，而不是第二个向量，作为魔术方法的参数。可以按以下方式完成：

```py
def __mul__(self, scalar):
    return (self.x * scalar, self.y * scalar)
def __div__(self, scalar):
    return (self.x / scalar, self.y / scalar)
```

# 向量否定和相等

由于我们已经涵盖了向量的最重要的操作，如加法、乘法和减法，我们现在将学习简单但重要的向量操作技术，即向量否定和相等。当玩家想要从当前状态到达前一个状态时（因为 AB = -BA），向量否定就变得很重要，这意味着否定任何向量都会创建另一个大小相同但方向相反的向量。为了否定一个向量，我们可以简单地向向量的每个分量添加`-`负运算符。例如，我们可以考虑以下代码行：

```py
def __neg__(self):
    return (–self.x, –self.y)
```

我们可以通过检查向量的每个分量来检查两个向量是否相等。例如，`first.x`应该与`second.x`进行比较，`first.y`应该与`second.y`进行比较。例如，以下方法将在两个向量相等时返回`True`：

```py
def __eq__(self, other):

        """v.__eq__(w) -> v == w
 >>> v = vector(1, 2)
 >>> w = vector(1, 2)
 >>> v == w
 True
 """

 if isinstance(other, vector):
            return self.x == other.x and self.y == other.y
        return NotImplemented
```

根据 Python 官方文档：

*（"`NotImplemented` *向运行时发出信号，告诉它应该要求其他人满足操作。在表达式`a == b`中，如果`a.__eq__(b)`返回* `NotImplemented`*，那么 Python 会尝试* `b.__eq__(a)`。如果 b 知道足够返回* `True` *或* `False`*，那么表达式可以成功。如果不知道，那么运行时将退回到内置行为（基于==和！=的身份）"）*。

# 总结

在本章中，我们涵盖了各种主题，从数据模型到向量的创建和操作。向量无疑是任何游戏开发者最重要的主题；它们帮助创建游戏角色和精灵的运动，使游戏更具用户互动性。我们学习了不同的操作，如加法、减法、除法、否定等。我们还使用这些操作和魔术方法来操作我们的向量组件。魔术方法是方法重写的一部分，应该在第六章 *面向对象编程*中介绍。然而，我将它保留到了这一章，因为在探索向量时学习它更有意义。

由于关于向量的数学逻辑是游戏中角色移动的主要基础，您已经学会了如何使用魔术函数实现运算符重载。本章学到的向量操作技能很重要，因为它们指定了对象的位置，并帮助我们执行一些代数运算。

本章向我们介绍了二维向量——一种数学概念，使得游戏中角色的运动成为可能。为了实现这一点，我们必须使用魔术函数的数据重载概念。为了重载任何运算符——即改变任何运算符的实现，比如`+`或`-`——我们将这些运算符的使用从原始数据类型扩展到复杂数据结构。本章的主要目标是向您介绍使用 Python 编程范式实现 2D 向量运算等数学概念的方法。

在下一章中，我们将利用本章的知识，使用海龟模块进行游戏编程的过山车之旅。我们将制作多个游戏，如贪吃蛇、乒乓球和 Flappy Bird。现在，是时候让您开始尝试向量的实验了；尝试将它们混合在一起，并为向量开发各种运动。
